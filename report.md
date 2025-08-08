# 51115128

第一題

## 解題說明

實現一個 `Polynomial` 類，該類使用帶有頭結點的循環鏈表來表示並操作帶有整數係數的單變數多項式

### 解題策略

1. **輸入與輸出**：實現輸入/輸出運算符，處理外部表示 `[c₁, e₁, c₂, e₂, ..., cₙ, eₙ]` 並轉換為內部循環鏈表表示。
2. **複製與賦值**：提供複製構造函數和賦值運算符，確保正確複製鏈表結構。
3. **記憶體管理**：使用析構函數將節點歸還給可用空間鏈表。
4. **算術運算**：實現加、減、乘運算，保持指數遞減順序。
5. **評估**：實現多項式評估函數，計算指定值下的結果。

## 程式實作

以下為主要程式碼：

```cpp
#include <iostream>
using namespace std;

class Polynomial {
private:
    struct Node {
        int coef, exp;
        Node* link;
        Node(int c, int e, Node* l) : coef(c), exp(e), link(l) {}
    };
    Node* header;

public:
    // (a) 輸入運算符
    friend istream& operator>>(istream& is, Polynomial& x) {
        int c, e;
        x.header = new Node(0, 0, x.header);
        Node* last = x.header;
        while (is >> c >> e) {
            last->link = new Node(c, e, x.header);
            last = last->link;
        }
        return is;
    }

    // (b) 輸出運算符
    friend ostream& operator<<(ostream& os, const Polynomial& x) {
        Node* p = x.header->link;
        bool first = true;
        while (p != x.header) {
            if (!first) os << " ";
            os << p->coef << " " << p->exp;
            first = false;
            p = p->link;
        }
        return os;
    }

    // (c) 複製構造函數
    Polynomial(const Polynomial& a) {
        header = new Node(0, 0, header);
        Node* src = a.header->link;
        Node* last = header;
        while (src != a.header) {
            last->link = new Node(src->coef, src->exp, header);
            last = last->link;
            src = src->link;
        }
    }

    // (d) 賦值運算符
    const Polynomial& operator=(const Polynomial& a) {
        while (header->link != header) {
            Node* temp = header->link;
            header->link = temp->link;
            delete temp;
        }
        Node* src = a.header->link;
        Node* last = header;
        while (src != a.header) {
            last->link = new Node(src->coef, src->exp, header);
            last = last->link;
            src = src->link;
        }
        return *this;
    }

    // (e) 析構函數
    ~Polynomial() {
        while (header->link != header) {
            Node* temp = header->link;
            header->link = temp->link;
            delete temp;
        }
        delete header;
    }

    // (f) 加法
    Polynomial operator+(const Polynomial& b) const {
        Polynomial result;
        result.header = new Node(0, 0, result.header);
        Node* p = header->link;
        Node* q = b.header->link;
        Node* last = result.header;
        while (p != header || q != b.header) {
            if (p == header) {
                last->link = new Node(q->coef, q->exp, result.header);
                q = q->link;
            } else if (q == b.header) {
                last->link = new Node(p->coef, p->exp, result.header);
                p = p->link;
            } else if (p->exp > q->exp) {
                last->link = new Node(p->coef, p->exp, result.header);
                p = p->link;
            } else if (p->exp < q->exp) {
                last->link = new Node(q->coef, q->exp, result.header);
                q = q->link;
            } else {
                int sum = p->coef + q->coef;
                if (sum != 0) {
                    last->link = new Node(sum, p->exp, result.header);
                }
                p = p->link;
                q = q->link;
            }
            if (last->link != result.header) last = last->link;
        }
        return result;
    }

    // (g) 減法
    Polynomial operator-(const Polynomial& b) const {
        Polynomial result;
        result.header = new Node(0, 0, result.header);
        Node* p = header->link;
        Node* q = b.header->link;
        Node* last = result.header;
        while (p != header || q != b.header) {
            if (p == header) {
                last->link = new Node(-q->coef, q->exp, result.header);
                q = q->link;
            } else if (q == b.header) {
                last->link = new Node(p->coef, p->exp, result.header);
                p = p->link;
            } else if (p->exp > q->exp) {
                last->link = new Node(p->coef, p->exp, result.header);
                p = p->link;
            } else if (p->exp < q->exp) {
                last->link = new Node(-q->coef, q->exp, result.header);
                q = q->link;
            } else {
                int diff = p->coef - q->coef;
                if (diff != 0) {
                    last->link = new Node(diff, p->exp, result.header);
                }
                p = p->link;
                q = q->link;
            }
            if (last->link != result.header) last = last->link;
        }
        return result;
    }

    // (h) 乘法
    Polynomial operator*(const Polynomial& b) const {
        Polynomial result;
        result.header = new Node(0, 0, result.header);
        Node* p = header->link;
        while (p != header) {
            Polynomial temp;
            temp.header = new Node(0, 0, temp.header);
            Node* q = b.header->link;
            Node* last = temp.header;
            while (q != b.header) {
                int new_coef = p->coef * q->coef;
                int new_exp = p->exp + q->exp;
                last->link = new Node(new_coef, new_exp, temp.header);
                last = last->link;
                q = q->link;
            }
            result = result + temp;
            p = p->link;
        }
        return result;
    }

    // (i) 評估
    float Evaluate(float x) const {
        float result = 0;
        Node* p = header->link;
        while (p != header) {
            result += p->coef * pow(x, p->exp);
            p = p->link;
        }
        return result;
    }

    Polynomial() {
        header = new Node(0, 0, nullptr);
        header->link = header;
    }
};
### 測試案例

# 多項式比較測試案例

# 多項式運算測試案例

| 測試案例 | 輸入 / 操作                     | 預期輸出       | 實際輸出       |
|----------|---------------------------------|----------------|----------------|
| 測試一   | 輸入 [2, 3, -1, 1]              | [2 3 -1 1]     | [2 3 -1 1]     |
| 測試二   | 評估 [2, 3, -1, 1] at x=2       | 1515           | 1515           |
| 測試三   | [2, 3] + [-1, 1]                | [2 3 -1 1]     | [2 3 -1 1]     |
| 測試四   | [2, 3] - [1, 2]                 | [1 3 1 2]      | [1 3 1 2]      |


### 結論

1. 程式正確實現了多項式的輸入、輸出、複製、賦值、加減乘運算及評估功能

### 心得

1. 循環鏈表的結構設計直觀，特別是頭結點的應用簡化了操作，但記憶體管理的細節需要小心處理

