---
layout: post
redirect_from:
  - /writeup/ctf/2017/teaser-confidence-ctf-2017-starblind/
  - /blog/2017/04/04/teaser-confidence-ctf-2017-starblind/
date: "2017-04-04T10:32:29+09:00"
tags: [ "ctf", "writeup", "rev" ]
"target_url": [ "https://ctftime.org/event/433" ]
---

# Teaser CONFidence CTF 2017: starblind

## solution

URLが与えられる。
html中にbase64された大きなjavascriptがあるのでこれを読む。
`CalcSHA4`という関数があるので、この関数が指定された出力を吐くような入力が`gGoodPassword`であるらしい。

`CalcSHA4`は単に逆関数を書けばよい。
`xor`も`perm`も自明な全単射になっている。
JavaScriptの`-0`は`0`でないので嵌ったし、そもそもやるだけなのでPythonに移す必要はなかった。

## implementation

``` python
#!/usr/bin/env python3
import math
import binascii

def xor(a, b):
    assert len(a) == 64
    assert len(b) == 64
    c = bytearray(64)
    for i in range(64):
        c[i] = a[i] ^ b[i]
    return c

def sgn(a):
    return math.copysign(1, a) < 0  # `a' may be a `-0.0'

def perm(a, b):
    assert len(a) == 64
    assert len(b) == 512
    c = bytearray(64)
    for i in range(512):
        dst_bit = i % 8
        dst_byte = i // 8
        sign = sgn(b[i])
        idx = abs(int(b[i]))
        src_bit = idx % 8
        src_byte = idx // 8
        assert 0 <= src_byte < 64
        assert 0 <= src_bit < 8
        t = (a[src_byte] >> src_bit) & 1
        if sign:
            t ^= 1
        assert 0 <= dst_byte < 64
        assert 0 <= dst_bit < 8
        c[dst_byte] |= t << dst_bit
    return c

def hexdigest(s):
    assert isinstance(s, bytearray)
    return ''.join([ '%02x' % c for c in s ])

def unhexdigest(s):
    assert isinstance(s, str)
    return bytearray(binascii.unhexlify(s))

def calc_sha4(block):
    assert isinstance(block, str)
    assert len(block) == 27

    r = bytearray(64)
    for i, c in enumerate(block):
        r[i] = ord(c)
    for i in range(32, 64):
        r[i] = i * 48271 % 256

    r = xor(r, [153,117,136,188,235,71,159,170,183,140,126,4,198,28,105,58,145,221,197,225,27,165,124,68,218,100,109,212,240,235,38,126,18,32,135,108,113,251,19,182,242,130,77,169,57,232,133,232,8,141,235,211,113,59,102,230,11,160,216,105,209,4,82,210])
    r = perm(r, [408,-511,-243,-164,493,12,-309,-172,358,158,-344,-46,326,-322,497,-8,91,-395,235,-36,-448,223,-403,57,-42,-388,-269,447,-435,186,100,425,-26,432,-360,370,-191,181,142,-401,15,211,-421,-109,342,-504,-288,-444,-264,-397,-384,298,-98,240,-292,-49,-256,362,-490,428,-193,347,510,-204,-449,-263,-253,33,-4,509,381,-66,220,113,67,-93,-126,-219,-495,406,-429,232,-224,90,64,-32,373,-23,92,-459,114,329,319,-0.0,-279,-404,442,-505,-361,104,-30,-423,-350,-251,-467,-133,-254,434,29,-476,498,-75,-234,185,152,97,-160,163,337,268,82,212,201,-426,-311,217,267,318,-69,-349,-378,287,-41,-131,227,157,332,-500,-297,-456,-139,377,462,-491,-2,455,247,-405,321,-24,-159,-118,-454,-180,280,416,108,-156,-124,452,-411,359,-457,71,-183,218,-242,-61,-258,492,317,-55,-9,-272,198,-226,478,302,146,-182,327,-47,99,-305,346,324,87,357,22,21,437,-231,-364,166,-130,289,-453,190,17,367,282,68,-141,-310,-281,303,215,120,-78,323,153,-368,-328,-72,-7,-334,410,-481,44,62,-458,246,150,244,482,86,-488,103,202,424,-402,197,276,-471,-177,252,293,-65,-200,-37,70,-122,-306,-431,-387,-39,-213,-203,299,-145,-119,-96,-73,94,339,-105,-60,-132,53,-308,18,393,-225,-241,-162,-278,430,-294,506,84,-259,-168,-161,-479,-85,-248,101,-116,-249,-261,-175,400,503,-314,501,54,-56,-409,-6,-79,34,14,-438,464,236,216,-208,-206,-507,351,-390,107,333,-194,353,-127,77,-52,371,3,-300,385,304,420,277,469,394,-28,352,399,-295,386,-250,366,475,463,102,-468,-439,417,-128,-214,-135,446,-125,-496,407,-184,341,-487,-136,-375,-167,-380,-284,16,-176,-398,48,-283,189,-147,58,-5,76,-266,382,-474,415,245,-313,199,470,-209,38,20,315,418,-379,-81,-148,392,494,330,296,461,239,1,123,-222,-165,499,-325,-27,-291,40,433,-51,196,-343,502,483,262,-233,31,-169,414,-134,-121,-111,-508,-436,-238,-485,-369,230,473,174,89,270,138,95,-374,-151,-35,-465,-274,338,210,-331,-365,-59,188,-320,-149,110,413,-355,451,-290,-316,-187,74,178,-229,13,-106,-117,-140,-144,-312,-286,173,-80,-480,-63,-43,427,412,-348,396,440,154,-472,-307,50,-356,257,25,112,-336,-170,207,-460,-419,-340,10,-115,-192,-273,-376,441,83,195,285,-11,-171,-260,19,335,-372,237,391,143,450,205,-477,-45,-255,-275,484,443,-363,228,137,179,-422,265,466,-389,155,486,88,-383,354,445,221,489,271,-301,-345,129])
    r = xor(r, [48,18,109,193,30,99,159,122,174,240,197,57,91,162,76,160,87,7,224,20,2,226,241,141,194,10,250,52,192,26,8,226,253,110,175,222,92,24,236,33,136,226,64,139,100,136,50,87,118,174,165,230,23,5,168,232,102,113,230,147,226,157,216,27])

    *snip*

    r = xor(r, [156,122,52,158,166,15,242,178,35,114,76,95,168,205,29,230,171,125,152,40,242,150,185,4,219,222,98,53,30,52,139,126,198,72,27,82,241,132,56,99,83,216,60,114,235,142,158,233,169,33,60,67,135,250,192,223,19,63,135,245,79,10,26,189])
    r = perm(r, [290,220,-463,440,51,-331,452,-93,-312,330,-436,7,-479,-254,29,406,150,-449,-161,-508,260,75,391,-261,-61,-344,402,-306,140,157,-395,-488,-404,-206,-136,-265,499,73,-501,-122,-174,245,-375,65,139,-109,-358,-210,340,-473,167,-333,-301,-224,472,267,-386,-430,-461,280,85,425,-133,-355,66,-56,223,32,-411,226,227,-72,-118,-180,34,-327,-424,426,-435,92,-477,101,191,163,493,-221,12,-103,110,496,-466,-19,14,453,-234,420,428,-144,304,388,-87,263,222,-442,276,-96,495,250,400,295,293,-445,318,370,-494,120,20,-175,464,-119,415,294,237,-5,-321,-1,-387,289,15,416,-219,86,302,-249,217,244,-429,172,187,279,-474,365,-458,-351,173,484,322,-398,-343,283,-377,-235,485,18,-308,148,320,155,497,-107,129,-216,91,252,-489,-380,-257,-366,379,-284,55,84,262,160,-350,507,352,315,-314,95,-124,-68,-506,-30,-346,-106,-37,-335,-287,270,-292,490,3,-447,128,-64,-371,281,319,111,-137,178,381,491,-274,-264,-48,405,-192,67,58,338,229,500,-332,-33,-143,-258,-193,-374,277,199,-509,478,-342,-291,-360,176,-188,-273,-104,-307,236,-38,438,372,246,-459,-50,62,149,-317,41,-231,-243,-146,431,-17,-334,-209,385,11,329,325,-171,-492,-9,-275,-22,-156,-182,-198,-286,60,-6,-39,-310,455,-305,-83,-115,69,147,-52,-288,-228,-211,97,130,362,-200,-28,-214,-369,242,-45,419,-498,364,417,-393,-26,-108,81,76,471,-337,390,35,410,8,476,-162,-359,145,189,-240,153,486,268,-454,-36,165,166,-448,181,25,-367,177,-316,266,-272,241,422,152,-412,251,-134,-99,-247,116,59,21,70,392,27,89,74,79,462,105,-94,-71,-132,-259,78,90,213,467,-469,126,409,183,113,-324,-434,-239,-158,-194,502,470,326,439,24,-186,-437,-159,-114,-432,443,-195,397,313,451,-278,487,-460,46,208,43,77,-125,-63,345,131,-44,-475,-233,-204,201,-348,-396,282,238,207,-253,-202,-356,-218,102,123,57,300,336,-179,298,-88,168,-483,418,376,-154,311,-441,-468,-399,141,-465,-47,112,-384,-121,-357,151,-444,481,403,49,-40,-142,-421,382,-42,197,446,248,408,-215,10,363,127,-190,450,-23,505,184,-2,100,-31,-427,-170,341,299,-361,297,504,53,212,205,-0.0,285,-80,323,368,230,232,-328,-510,423,54,203,413,-414,225,82,373,138,309,296,-349,269,-347,255,-394,303,-401,271,-389,-378,-13,511,196,117,480,-383,456,169,-4,354,16,256,503,339,-164,457,-407,353,-135,98,433,185,482])

    return hexdigest(r)

correct = '983bb35ed0a800fcc85d12806df9225364713be578ba67f65bc508b77f0c54878eda18a5eed50bac705bdc7db205623221e8ffe330483955a22216960754a122'
# assert calc_sha4(password) == correct

assert calc_sha4('012345678901234567890123456') == '8829837592a80a9ecc1852506ef83255664334ed40ba2576db4f08967f0c54068cda5e87e8d50f9c62fb5a19b48562382160ff2710416b75aaa2069627549786'

def unperm(a, b):
    assert len(a) == 64
    assert len(b) == 512
    c = bytearray(64)
    for i in range(512):
        dst_bit = i % 8
        dst_byte = i // 8
        sign = sgn(b[i])
        idx = abs(int(b[i]))
        src_bit = idx % 8
        src_byte = idx // 8
        assert 0 <= dst_byte < 64
        assert 0 <= dst_bit < 8
        t = (a[dst_byte] >> dst_bit) & 1
        if sign:
            t ^= 1
        assert 0 <= src_byte < 64
        assert 0 <= src_bit < 8
        c[src_byte] |= t << src_bit
    return c

r = unhexdigest(correct)
perm_array = [290,220,-463,440,51,-331,452,-93,-312,330,-436,7,-479,-254,29,406,150,-449,-161,-508,260,75,391,-261,-61,-344,402,-306,140,157,-395,-488,-404,-206,-136,-265,499,73,-501,-122,-174,245,-375,65,139,-109,-358,-210,340,-473,167,-333,-301,-224,472,267,-386,-430,-461,280,85,425,-133,-355,66,-56,223,32,-411,226,227,-72,-118,-180,34,-327,-424,426,-435,92,-477,101,191,163,493,-221,12,-103,110,496,-466,-19,14,453,-234,420,428,-144,304,388,-87,263,222,-442,276,-96,495,250,400,295,293,-445,318,370,-494,120,20,-175,464,-119,415,294,237,-5,-321,-1,-387,289,15,416,-219,86,302,-249,217,244,-429,172,187,279,-474,365,-458,-351,173,484,322,-398,-343,283,-377,-235,485,18,-308,148,320,155,497,-107,129,-216,91,252,-489,-380,-257,-366,379,-284,55,84,262,160,-350,507,352,315,-314,95,-124,-68,-506,-30,-346,-106,-37,-335,-287,270,-292,490,3,-447,128,-64,-371,281,319,111,-137,178,381,491,-274,-264,-48,405,-192,67,58,338,229,500,-332,-33,-143,-258,-193,-374,277,199,-509,478,-342,-291,-360,176,-188,-273,-104,-307,236,-38,438,372,246,-459,-50,62,149,-317,41,-231,-243,-146,431,-17,-334,-209,385,11,329,325,-171,-492,-9,-275,-22,-156,-182,-198,-286,60,-6,-39,-310,455,-305,-83,-115,69,147,-52,-288,-228,-211,97,130,362,-200,-28,-214,-369,242,-45,419,-498,364,417,-393,-26,-108,81,76,471,-337,390,35,410,8,476,-162,-359,145,189,-240,153,486,268,-454,-36,165,166,-448,181,25,-367,177,-316,266,-272,241,422,152,-412,251,-134,-99,-247,116,59,21,70,392,27,89,74,79,462,105,-94,-71,-132,-259,78,90,213,467,-469,126,409,183,113,-324,-434,-239,-158,-194,502,470,326,439,24,-186,-437,-159,-114,-432,443,-195,397,313,451,-278,487,-460,46,208,43,77,-125,-63,345,131,-44,-475,-233,-204,201,-348,-396,282,238,207,-253,-202,-356,-218,102,123,57,300,336,-179,298,-88,168,-483,418,376,-154,311,-441,-468,-399,141,-465,-47,112,-384,-121,-357,151,-444,481,403,49,-40,-142,-421,382,-42,197,446,248,408,-215,10,363,127,-190,450,-23,505,184,-2,100,-31,-427,-170,341,299,-361,297,504,53,212,205,-0.0,285,-80,323,368,230,232,-328,-510,423,54,203,413,-414,225,82,373,138,309,296,-349,269,-347,255,-394,303,-401,271,-389,-378,-13,511,196,117,480,-383,456,169,-4,354,16,256,503,339,-164,457,-407,353,-135,98,433,185,482]
r = unperm(r, perm_array)
r = perm(r, perm_array)
assert hexdigest(r) == correct

def reverse_sha4(s):
    r = unhexdigest(s)

    r = unperm(r, [290,220,-463,440,51,-331,452,-93,-312,330,-436,7,-479,-254,29,406,150,-449,-161,-508,260,75,391,-261,-61,-344,402,-306,140,157,-395,-488,-404,-206,-136,-265,499,73,-501,-122,-174,245,-375,65,139,-109,-358,-210,340,-473,167,-333,-301,-224,472,267,-386,-430,-461,280,85,425,-133,-355,66,-56,223,32,-411,226,227,-72,-118,-180,34,-327,-424,426,-435,92,-477,101,191,163,493,-221,12,-103,110,496,-466,-19,14,453,-234,420,428,-144,304,388,-87,263,222,-442,276,-96,495,250,400,295,293,-445,318,370,-494,120,20,-175,464,-119,415,294,237,-5,-321,-1,-387,289,15,416,-219,86,302,-249,217,244,-429,172,187,279,-474,365,-458,-351,173,484,322,-398,-343,283,-377,-235,485,18,-308,148,320,155,497,-107,129,-216,91,252,-489,-380,-257,-366,379,-284,55,84,262,160,-350,507,352,315,-314,95,-124,-68,-506,-30,-346,-106,-37,-335,-287,270,-292,490,3,-447,128,-64,-371,281,319,111,-137,178,381,491,-274,-264,-48,405,-192,67,58,338,229,500,-332,-33,-143,-258,-193,-374,277,199,-509,478,-342,-291,-360,176,-188,-273,-104,-307,236,-38,438,372,246,-459,-50,62,149,-317,41,-231,-243,-146,431,-17,-334,-209,385,11,329,325,-171,-492,-9,-275,-22,-156,-182,-198,-286,60,-6,-39,-310,455,-305,-83,-115,69,147,-52,-288,-228,-211,97,130,362,-200,-28,-214,-369,242,-45,419,-498,364,417,-393,-26,-108,81,76,471,-337,390,35,410,8,476,-162,-359,145,189,-240,153,486,268,-454,-36,165,166,-448,181,25,-367,177,-316,266,-272,241,422,152,-412,251,-134,-99,-247,116,59,21,70,392,27,89,74,79,462,105,-94,-71,-132,-259,78,90,213,467,-469,126,409,183,113,-324,-434,-239,-158,-194,502,470,326,439,24,-186,-437,-159,-114,-432,443,-195,397,313,451,-278,487,-460,46,208,43,77,-125,-63,345,131,-44,-475,-233,-204,201,-348,-396,282,238,207,-253,-202,-356,-218,102,123,57,300,336,-179,298,-88,168,-483,418,376,-154,311,-441,-468,-399,141,-465,-47,112,-384,-121,-357,151,-444,481,403,49,-40,-142,-421,382,-42,197,446,248,408,-215,10,363,127,-190,450,-23,505,184,-2,100,-31,-427,-170,341,299,-361,297,504,53,212,205,-0.0,285,-80,323,368,230,232,-328,-510,423,54,203,413,-414,225,82,373,138,309,296,-349,269,-347,255,-394,303,-401,271,-389,-378,-13,511,196,117,480,-383,456,169,-4,354,16,256,503,339,-164,457,-407,353,-135,98,433,185,482])
    r = xor(r, [156,122,52,158,166,15,242,178,35,114,76,95,168,205,29,230,171,125,152,40,242,150,185,4,219,222,98,53,30,52,139,126,198,72,27,82,241,132,56,99,83,216,60,114,235,142,158,233,169,33,60,67,135,250,192,223,19,63,135,245,79,10,26,189])

    *snip*

    r = unperm(r, [408,-511,-243,-164,493,12,-309,-172,358,158,-344,-46,326,-322,497,-8,91,-395,235,-36,-448,223,-403,57,-42,-388,-269,447,-435,186,100,425,-26,432,-360,370,-191,181,142,-401,15,211,-421,-109,342,-504,-288,-444,-264,-397,-384,298,-98,240,-292,-49,-256,362,-490,428,-193,347,510,-204,-449,-263,-253,33,-4,509,381,-66,220,113,67,-93,-126,-219,-495,406,-429,232,-224,90,64,-32,373,-23,92,-459,114,329,319,-0.0,-279,-404,442,-505,-361,104,-30,-423,-350,-251,-467,-133,-254,434,29,-476,498,-75,-234,185,152,97,-160,163,337,268,82,212,201,-426,-311,217,267,318,-69,-349,-378,287,-41,-131,227,157,332,-500,-297,-456,-139,377,462,-491,-2,455,247,-405,321,-24,-159,-118,-454,-180,280,416,108,-156,-124,452,-411,359,-457,71,-183,218,-242,-61,-258,492,317,-55,-9,-272,198,-226,478,302,146,-182,327,-47,99,-305,346,324,87,357,22,21,437,-231,-364,166,-130,289,-453,190,17,367,282,68,-141,-310,-281,303,215,120,-78,323,153,-368,-328,-72,-7,-334,410,-481,44,62,-458,246,150,244,482,86,-488,103,202,424,-402,197,276,-471,-177,252,293,-65,-200,-37,70,-122,-306,-431,-387,-39,-213,-203,299,-145,-119,-96,-73,94,339,-105,-60,-132,53,-308,18,393,-225,-241,-162,-278,430,-294,506,84,-259,-168,-161,-479,-85,-248,101,-116,-249,-261,-175,400,503,-314,501,54,-56,-409,-6,-79,34,14,-438,464,236,216,-208,-206,-507,351,-390,107,333,-194,353,-127,77,-52,371,3,-300,385,304,420,277,469,394,-28,352,399,-295,386,-250,366,475,463,102,-468,-439,417,-128,-214,-135,446,-125,-496,407,-184,341,-487,-136,-375,-167,-380,-284,16,-176,-398,48,-283,189,-147,58,-5,76,-266,382,-474,415,245,-313,199,470,-209,38,20,315,418,-379,-81,-148,392,494,330,296,461,239,1,123,-222,-165,499,-325,-27,-291,40,433,-51,196,-343,502,483,262,-233,31,-169,414,-134,-121,-111,-508,-436,-238,-485,-369,230,473,174,89,270,138,95,-374,-151,-35,-465,-274,338,210,-331,-365,-59,188,-320,-149,110,413,-355,451,-290,-316,-187,74,178,-229,13,-106,-117,-140,-144,-312,-286,173,-80,-480,-63,-43,427,412,-348,396,440,154,-472,-307,50,-356,257,25,112,-336,-170,207,-460,-419,-340,10,-115,-192,-273,-376,441,83,195,285,-11,-171,-260,19,335,-372,237,391,143,450,205,-477,-45,-255,-275,484,443,-363,228,137,179,-422,265,466,-389,155,486,88,-383,354,445,221,489,271,-301,-345,129])
    r = xor(r, [153,117,136,188,235,71,159,170,183,140,126,4,198,28,105,58,145,221,197,225,27,165,124,68,218,100,109,212,240,235,38,126,18,32,135,108,113,251,19,182,242,130,77,169,57,232,133,232,8,141,235,211,113,59,102,230,11,160,216,105,209,4,82,210])

    return r

print(reverse_sha4(correct))
```