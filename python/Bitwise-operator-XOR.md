# Bitwise operator XOR

python은 다양한 Bitwise operator를 제공한다.

AND, OR, NOT 등은 쉽게 이해가 되는데 `XOR`이 이해가 잘 안 되어서 이번 기회에 정리해봄.


| operator | name | description                           |
| &        | AND  | bit가 모두 1일 경우 1                 |
| \|       | OR   | bit 중 1인게 하나로도 있을 경우 1     |
| ~        | NOT  | 모든 bit를 invert (반대로)            |
| ^        | XOR  | 하나의 bit만 1일 경우 1               |


AND, OR, NOT 등 의미상으로만 이해헀는데 bit를 대입해서 이해하니 훨씬 이해가 쉬웠다. 실제로 내부적으로 bit연산으로 비교를 한다고 하여 numpy 등에서는 bitwise operator를 주로 사용하면 좋을듯.
