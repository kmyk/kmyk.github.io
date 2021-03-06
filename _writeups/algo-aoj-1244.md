---
layout: post
redirect_from:
  - /writeup/algo/aoj/1244/
  - /blog/2017/10/03/aoj-1244/
date: "2017-10-03T06:10:25+09:00"
tags: [ "competitive", "writeup", "aoj", "icpc", "icpc-asia", "parsing" ]
---

# AOJ 1244. Molecular Formula

「先輩のコードは見た目が重たい」と言われた。
競プロ始めたころから気になってはいるが、`int parse_molecules(string::const_iterator & first, string::const_iterator last, map<string, int> const & weight);`と全部global変数な`int MO(char *p);`とだと前者を選びたい気がするし、かといってclassを切るのはそれはそれで重いので困っている。

## solution

再帰下降構文解析。Moleculeの規則が左再帰を含むのでいい感じに除去する。入力全体の文字数$N$に対し$O(N)$。

## implementation

``` c++
#include <cassert>
#include <cctype>
#include <iostream>
#include <map>
using namespace std;

struct unknown_atom_exception {};
int parse_number(string::const_iterator & first, string::const_iterator last) {
    assert (first != last);
    int n = 0;
    while (first != last and isdigit(*first)) {
        n *= 10;
        n += *first - '0';
        ++ first;
    }
    return n;
}
string parse_atom(string::const_iterator & first, string::const_iterator last) {
    assert (first != last);
    assert (isupper(*first));
    string name;
    name += *first;
    ++ first;
    if (first != last and islower(*first)) {
        name += *first;
        ++ first;
    }
    return name;
}
int parse_molecules(string::const_iterator & first, string::const_iterator last, map<string, int> const & weight);
int parse_molecule(string::const_iterator & first, string::const_iterator last, map<string, int> const & weight) {
    assert (first != last);
    int value;
    if (isalpha(*first)) {
        string atom = parse_atom(first, last);
        if (weight.count(atom)) {
            value = weight.at(atom);
        } else {
            throw unknown_atom_exception {};
        }
    } else {
        assert (*first == '(');
        ++ first;
        value = parse_molecules(first, last, weight);
        assert (*first == ')');
        ++ first;
    }
    if (first != last and isdigit(*first)) {
        value *= parse_number(first, last);
    }
    return value;
}
int parse_molecules(string::const_iterator & first, string::const_iterator last, map<string, int> const & weight) {
    assert (first != last);
    int value = 0;
    while (first != last and *first != ')') {
        value += parse_molecule(first, last, weight);
    }
    return value;
}
int parse(string const & s, map<string, int> const & weight) {
    try {
        auto it = s.begin();
        return parse_molecules(it, s.end(), weight);
    } catch (unknown_atom_exception e) {
        return -1;
    }
}

int main() {
    map<string, int> weight;
    while (true) {
        string atom; cin >> atom;
        if (atom == "END_OF_FIRST_PART") break;
        cin >> weight[atom];
    }
    while (true) {
        string expr; cin >> expr;
        if (expr == "0") break;
        int result = parse(expr, weight);
        if (result == -1) {
            cout << "UNKNOWN" << endl;
        } else {
            cout << result << endl;
        }
    }
    return 0;
}
```
