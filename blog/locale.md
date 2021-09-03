## locale命令

locale这个单词中文翻译成地区或者地域，其实这个单词包含的意义要宽泛很多。Locale是根据计算机用户所使用的语言，所在国家或者地区，以及当地的文化传统所定义的一个软件运行时的语言环境。

```
$ locale
LANG=en_US.UTF-8
LANGUAGE=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8
```

## locale分类

locale把按照所涉及到的文化传统的各个方面分成12个大类，这12个大类分别是：

1. 语言符号及其分类(LC_CTYPE) 
2. 数字(LC_NUMERIC) 
3. 比较和排序习惯(LC_COLLATE) 
4. 时间显示格式(LC_TIME) 
5. 货币单位(LC_MONETARY) 
6. 信息主要是提示信息,错误信息,状态信息,标题,标签,按钮和菜单等(LC_MESSAGES) 
7. 姓名书写方式(LC_NAME) 
8. 地址书写方式(LC_ADDRESS) 
9. 电话号码书写方式(LC_TELEPHONE) 
10. 度量衡表达方式 (LC_MEASUREMENT) 
11. 默认纸张尺寸大小(LC_PAPER) 
12. 对locale自身包含信息的概述(LC_IDENTIFICATION)。

所以说，locale就是某一个地域内的人们的语言习惯和文化传统和生活习惯。

## 字符集

上面我们说到了zh_CN.GB18030的前半部分，后半部分是什么呢？大部分Linux用户都知道是系统采用的字符集。

zh_CN.GB2312到底是在说什么？ Locale是软件在运行时的语言环境, 它包括语言(Language), 地域 (Territory) 和字符集(Codeset)。一个locale的书写格式为: `语言[_地域[.字符集]]`。所以说呢，locale总是和一定的字符集相联系的。下面举几个例子：

1. 我说中文，身处中华人民共和国，使用国标2312字符集来表达字符。zh_CN.GB2312＝中文_中华人民共和国＋国标2312字符集。 
2. 我说中文，身处中华人民共和国，使用国标18030字符集来表达字符。zh_CN.GB18030＝中文_中华人民共和国＋国标18030字符集。 
3. 我说中文，身处中华人民共和国台湾省，使用国标Big5字符集来表达字符。zh_TW.BIG5=中文_台湾.大五码字符集 
4. 我说英文，身处大不列颠，使用ISO-8859-1字符集来表达字符。 en_GB.ISO-8859-1=英文_大不列颠.ISO-8859-1字符集 
5. 我说德语，身处德国，使用UTF-8字符集，习惯了欧洲风格。`de_DE.UTF-8@euro`＝德语_德国.UTF-8字符集@按照欧洲习惯加以修正，注意不是`de_DE@euro.UTF-8`，所以完全的locale表达方式是 `[语言[_地域][.字符集] [@修正值]`。

其中，与中文输入关系最密切的就是LC_CTYPE，LC_CTYPE规定了系统内有效的字符以及这些字符的分类，诸如什么是大写字母，小写字母，大小写转换，标点符号、可打印字符和其他的字符属性等方面。而locale定 义zh_CN中最最重要的一项就是定义了汉字(Class“hanzi”)这一个大类，当然也是用Unicode描述的，这就让中文字符在Linux系统中成为合法的有效字符，而且不论它们是用什么字符集编码的。

## 怎样设定locale

设定locale就是设定12大类的locale分类属性，即12个`LC_*`。除了这12个变量可以设定以外，为了简便起见，还有两个变量：LC_ALL和LANG。它们之间有一个优先级的关系：`LC_ALL > LC_* >LANG`。可以这么说，LC_ALL是最上级设定或者强制设定，而LANG是默认设定值。

1. 如果你设定了LC_ALL＝zh_CN.UTF-8，那么不管`LC_*`和LANG设定成什么值，它们都会被强制服从LC_ALL的设定，成为 zh_CN.UTF-8。
2. 假如你设定了LANG＝zh_CN.UTF-8，而其他的`LC_*=en_US.UTF-8`，并且没有设定LC_ALL的话，那么系统的locale设定以`LC_*=en_US.UTF-8`。
3. 假如你设定了LANG＝zh_CN.UTF-8，而其他的`LC_*`，和LC_ALL均未设定的话，系统会将`LC_*`设定成默认值，也就是LANG的值zh_CN.UTF-8。
4. 假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_CTYPE=en_US.UTF-8，其他的`LC_*`，和LC_ALL均未设定的话，那么系统的locale设定将是：LC_CTYPE=en_US.UTF-8，其余的 LC_COLLATE，LC_MESSAGES等等均会采用默认值，也就是 LANG的值，也就是LC_COLLATE＝LC_MESSAGES＝……＝ LC_PAPER＝LANG＝zh_CN.UTF-8。

所以，locale是这样设定的： 

1. 如果你需要一个纯中文的系统的话，设定LC_ALL= zh_CN.XXXX，或者LANG=zh_CN.XXXX都可以，当然你可以两个都设定，但正如上面所讲，LC_ALL的值将覆盖所有其他的locale设定，不要作无用功。
2. 如果你只想要一个可以输入中文的环境，而保持菜单、标题，系统信息等等为英文界面，那么只需要设定 LC_CTYPE＝zh_CN.XXXX，LANG=en_US.XXXX就可以了。这样LC_CTYPE＝zh_CN.XXXX，而LC_COLLATE＝LC_MESSAGES＝……＝ LC_PAPER＝LANG＝en_US.XXXX。
3. 假如你高兴的话，可以把12个`LC_*`一一设定成你需要的值，打造一个古灵精怪的系统： LC_CTYPE＝zh_CN.GBK/GBK(使用中文编码内码GBK字符集)； LC_NUMERIC=en_GB.ISO-8859-1(使用大不列颠的数字系统) LC_MEASUREMEN=de_DE@euro.ISO-8859-15(德国的度量衡使用ISO-8859-15字符集) 罗马的地址书写方式，美国的纸张设定……。估计没人这么干吧。
4. 假如你什么也不做的话，也就是LC_ALL，LANG和`LC_*`均不指定特定值的话，系统将采用POSIX作为lcoale，也就是C locale。

## Local设定的命令

显示已安装locale：
```
locale -a
```

编译locale文件：
```
localedef -c -f UTF-8 -i zh_CN zh_CN.utf8 
```

设置编码：
```
export LC_ALL=zh_CN.utf8 
```

运行localedef --help能查看当前编码文件所在的路径。如果要永久更改编码，运行：
```
echo "export LC_ALL=zh_CN.utf8" >> /etc/profile 
```

## 系统默认locale

"C"是系统默认的locale，"POSIX"是"C"的别名。所以当我们新安装完一个系统时，默认的locale就是C或POSIX。

"POSIX" ：Specifies the minimal environmentfor C-language translation called the POSIX locale.If setlocale() is not invoked, the POSIXlocale is the default

"C"  Equivalentto "POSIX".

## LANG和LANGUAGE的区别

- LANG: Specifies the default locale for all unset locale variables
- LANGUAGE: Most programs use this for the language of its interface

LANGUAGE是设置应用程序的界面语言。而LANG是优先级很低的一个变量，它指定所有与locale有关的变量的默认值

## setlocale

```c++
int main() {
    // char *setlocale(int category, const char *locale);
    
    // If locale is "", each part of the locale that should be modified is
    // set according to the environment variables.
    setlocale(LC_ALL, "");
    
    // If locale is NULL, the current locale is only queried, not modified.
    std::cout << "LC_ALL: " << setlocale(LC_ALL, NULL) << std::endl;
    std::cout << "LC_CTYPE: " << setlocale(LC_CTYPE, NULL) << std::endl;

    return 0;
}
```

## References

1. [locale的设定及其LANG、LC_ALL、LANGUAGE环境变量的区别](https://blog.csdn.net/lidandan2016/article/details/85084872)
