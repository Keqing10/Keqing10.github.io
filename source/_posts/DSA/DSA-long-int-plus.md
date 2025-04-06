---
title: 长整数加法
category_bar: true
math: false
tags: [算法]
category: [数据结构与算法]
date: 2025-04-06 20:26:11
---

> *问题*：输入两个正整数，输出和。输入以字符串形式给出。

这里介绍两种思路。

## 通过`std::string`存储与逐位计算
适合处理中等长度的整数（例如几十位到几百位）。
```cpp
std::string addLongIntegers(const std::string& num1, const std::string& num2) {
    // 反转字符串，方便从低位到高位计算
    std::string reversedNum1 = num1;
    std::string reversedNum2 = num2;
    std::reverse(reversedNum1.begin(), reversedNum1.end());
    std::reverse(reversedNum2.begin(), reversedNum2.end());
   
    std::string result;  // 结果字符串
    int carry = 0;  // 进位
    int maxLength = std::max(reversedNum1.length(), reversedNum2.length());

    for (int i = 0; i < maxLength; ++i) {
        // 获取当前位的数字，如果超出长度则补 0
        int digit1 = (i < reversedNum1.length()) ? (reversedNum1[i] - '0') : 0;
        int digit2 = (i < reversedNum2.length()) ? (reversedNum2[i] - '0') : 0;

        // 计算当前位的和
        int sum = digit1 + digit2 + carry;
        carry = sum / 10; // 更新进位
        result.push_back((sum % 10) + '0'); // 将当前位的结果存入
    }
    // 如果最后还有进位，添加到结果中
    if (carry) result.push_back(carry + '0');
    // 反转结果，恢复正常顺序
    std::reverse(result.begin(), result.end());
    // 去除前导零（如果结果不是 "0"），保证输入为正时不需要
    // result.erase(0, result.find_first_not_of('0'));
    if (result.empty()) result = "0";
    return result;
}
```

### 通过`vector<int>`分段保存
适合处理较长的整数（例如几百位到几千位）。
```cpp
class LongInt {
public:
    static constexpr int M = 1e9;  // M进制分段，每段9位
    vector<int> vd;  // vd逆向保存结果
    LongInt() {}
    LongInt(int a) {
        while (a > 0) {
            vd.push_back(a % M);
            a /= M;
        }
    }
    LongInt(long long a) {
        while (a > 0) {
            vd.push_back(a % M);
            a /= M;
        }
    }
    LongInt(const string& num) {
        for (int i = num.size(); i > 0; i -= 9) {
            int start = max(0, i - 9);
            string chunkStr = num.substr(start, i - start);
            vd.push_back(stoi(chunkStr));
        }
    }

    string getString() {
        string result;
        for (int i = vd.size() - 1; i >= 0; --i) {
            string chunkStr = to_string(vd[i]);
            if (i != vd.size() - 1) {
                // 补前导零，确保每段都是 9 位
                chunkStr.insert(0, 9 - chunkStr.size(), '0');
            }
            result += chunkStr;
        }
        return result;
    }
    void output() {
        if (vd.empty()) printf("0");
        else {
            printf("%d", vd.back());
            for (int i = vd.size() - 2; i >= 0; --i) printf("%09d", vd[i]);
        }
    }

    LongInt operator+(const LongInt& other) {
        LongInt result;
        int carry = 0; // 进位
        int maxChunks = max(vd.size(), other.vd.size());
        for (int i = 0; i < maxChunks; ++i) {
            int chunk1 = (i < vd.size()) ? vd[i] : 0;
            int chunk2 = (i < other.vd.size()) ? other.vd[i] : 0;

            int sum = chunk1 + chunk2 + carry;
            carry = sum / M; // 每段最多 9 位，进位是 sum / 10^9
            result.vd.push_back(sum % M);
        }
        // 如果最后还有进位，添加到结果中
        if (carry) {
            result.vd.push_back(carry);
        }
        return result;
    }
};
```
使用示例。
```cpp
int main() {
    LongInt a("1234567853264901234567890"), b("98765432109876543210");
    LongInt c = a + b;
    cout << c.getString() << endl;
    c.output();
    return 0;
}
```
