# Unicode和char类型

## Unicode和char类型（P35）

**Unicode**

为解决多种编码表编码不同而设计。16位的char类型不能满足描述所有Unicode字符的需要。

**码点\(code point\)**

码点是指与一个编码表中的某个字符对应的代码值。在Unicode标准中，码点采用十六进制书写，并加上前缀U+，例如U+0041就是拉丁字母A的码点。Unicode的码点可以分成17个代码级别\(code plane\)。第一个代码级别称为基本的多语言级别\(basic multilingual plane\)，码点从U+0000到U+FFFF。其余的16个级别码点从U+10000到U+10FFFF，其中包括一些辅助字符\(supplementary character\)。

**UTF-16**

UTF-16编码采用不同长度的编码表示所有Unicode码点。在基本的多语言级别中，每个字符用16位表示，通常被称为代码单元\(code unit\)；而辅助字符采用一对连续的代码单元进行编码。这样构成的编码值落入基本的多语言级别中空闲的2048字节内，通常被称为替代区域\(surrogate area\)\[U+D800~U+DBFF用于第一个代码单元，U+DC00~U+DFFF用于第二个代码单元\]。

**char**

在Java中，char类型描述了UTF-16编码中的一个代码单元。大多数的常用Unicode字符使用一个代码单元就可以表示，而辅助字符需要一对代码单元表示。

字符串函数public char chartAt\(int i\)将获得位置为i的代码单元。

public int codePointAt\(int i\)将获得第i个码点。

强烈建议不要在程序中使用char类型，除非确实需要处理UTF-16代码单元。最好将字符串作为抽象数据类型处理。

遍历一个字符串，并且依次查看每一个码点，可以使用codePoints方法。它会生成一个int值的“流”，每个int值对应一个码点。可以将它转换为一个数组，再遍历

```text
int[] codePoints = str.codePoints().toArray();
```

要把一个码点数组转换为一个字符串，可以使用构造函数

```text
String str = new String(codePoints , 0 , codePoints.length);
```

