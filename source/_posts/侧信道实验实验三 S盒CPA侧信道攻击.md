---
title: S盒CPA侧信道攻击
date: 2021-12-24 00:11:30
categories: 
- SCA
tags: 
- experiment
---

---------------------------------------202/12/24 更新---------------------------------------

如若有图片不能查看，可复制图片链接以浏览。
---------------------------------------202/12/23 更新---------------------------------------
这个实验和的另一篇文章[侧信道实验实验二 S盒DPA侧信道攻击](https://blog.csdn.net/Reaper_MXBG/article/details/122019954)相似处很多，起码在完善Sboxcpa攻击-student.py程序阶段是一样的。因为很近似，所以一些细节我就没有赘述，想看更多细节的可以移步至我的另一篇文章。
-------------------------------------------起始内容-------------------------------------------

## 实验目的

1. 掌握S盒侧信道攻击的基本原理；
2. 掌握cpa攻击基本原理和方法。

## 实验人数

每组1人

## 系统环境

Windows

## 实验原理

1. 测量阶段，选取随机数量明文和设定好的正确密钥进行异或，得到的6bit结果作为S盒的输入，S盒的输出需统计二进制中1的个数，作为汉明重量，所有输出得到一个汉明重量数组。

2. 攻击阶段，需要遍历密钥的所有可能，每次遍历都需经历测量阶段，最后得到所有猜测密钥的汉明重量数组，且每个都需和正确密钥得到的汉明重量数组共同计算相关系数数组，公式如下
   $$
   corr=\frac{\sum_n^{}{\left(x_i-\overline{x}  \right)}\cdot \left( y_i-\overline{y} \right)}{\sqrt{\sum_n^{}{\left( x_i-\overline{x} \right) ^2}}\cdot \sqrt{\sum_n^{}{\left( y_i-\overline{y} \right) ^2}}}
   $$
   其中，$\overline{x}=\frac{1}{n}\sum_n^{}{x_i}$，$\overline{y}=\frac{1}{n}\sum_n^{}{y_i}$表示均值。

## 实验内容

1. 完成Sboxdpa仿真-student.py程序中的空缺部分

   - 补充n、plaintext、keyTrue

   - 补充HWfun函数，返回输入的汉明重量

   - 补充主函数，得到输出相应的汉明重量向量

2. 完成Sboxcpa攻击-student.py程序中的空缺部分：

   - 补充plaintext、power_std

   - 补充相关系数Corrfun函数，返回两个向量的相关系数

   - 补充主函数，猜测所有可能的密钥，对每个密钥计算相关系数，求出相关系数最大的猜测密钥

3. 实验测试2组数据

## 实验步骤

### 完善Sboxdpa仿真-student.py程序

#### 补充变量

补充n、plaintext、keyTrue三个变量

```python
n = 9	#第一组明文数组长度
#n = 64 #第二组明文数组长度
plainlist = [41, 35, 62, 4, 33, 44, 22, 46, 18]	#第一组明文数组
#plainlist = [3, 10, 3, 1, 9, 48, 2, 59, 50, 32, 7, 27, 27, 54, 32, 52, 39, 2, 11, 41, 42, 9, 7, 62, 63, 62, 56, 40, 19, 55, 57, 40, 7, 7, 37, 34, 15, 26, 18, 58, 32, 9, 56, 1, 42, 35, 60, 7, 43, 56, 26, 28, 16, 25, 2, 57, 47, 44, 62, 27, 9, 31, 6, 5] #第二组明文数组
keyTrue = 49	#加密所用密钥
```

#### 完善HWfun函数

此函数本质上是计算整数转化为二进制后中1的个数，可以使用快速统计的方法。

补充代码如下：

```python
def HWfun(num):
    # 统计输入num的汉明重量并返回
    ans = 0
    if num == 0:
        return 0
    while num > 0:
        num &= (num - 1)
        ans += 1
    return ans
```

#### 完善主函数

使用表格美化库完善输出。

补充代码如下：

```python
if __name__ == "__main__":
    # 补充：S盒输出对应汉明重量列表
    table = pt.PrettyTable()
    hw_std = []
    HWout = sboxout(n, plainlist, keyTrue)
    for i in range(n):
        hw_std.append(HWfun(HWout[i]))
    table.add_column('序号',[i for i in range(1,n+1)])
    table.add_column('明文-十进制',[index for index in plainlist])
    table.add_column('S盒输出-十进制', [index for index in HWout])
    table.add_column('S盒输出-汉明重量', [index for index in hw_std])
    print(table)
```

#### 程序运行结果

![3-1.1](https://img-blog.csdnimg.cn/img_convert/6a932427825b14d460c6bf6f10c76497.png#pic_center )

<center>
    <b>
        <small> 
            图6.1&nbsp&nbsp&nbsp&nbsp 长度为9的明文数组表格输出
        </small>
    </b>
</center>
![3-1.2](https://img-blog.csdnimg.cn/img_convert/f2c4c925bc3612620762618e8f0a6bcf.png#pic_center)

<center>
    <b>
        <small> 
            图6.2&nbsp&nbsp&nbsp&nbsp 长度为64的明文数组表格输出
        </small>
    </b>
</center>
### 完善Sboxcpa攻击-student.py程序

#### 补充变量

使用Sboxdpa仿真-student.py程序得到的结果：

```python
plainlist = [41, 35, 62, 4, 33, 44, 22, 46, 18]	#第一组明文数组
#plainlist = [3, 10, 3, 1, 9, 48, 2, 59, 50, 32, 7, 27, 27, 54, 32, 52, 39, 2, 11, 41, 42, 9, 7, 62, 63, 62, 56, 40, 19, 55, 57, 40, 7, 7, 37, 34, 15, 26, 18, 58, 32, 9, 56, 1, 42, 35, 60, 7, 43, 56, 26, 28, 16, 25, 2, 57, 47, 44, 62, 27, 9, 31, 6, 5] #第二组明文数组
power_std = [2, 2, 1, 2, 2, 2, 1, 1, 2] #第一组明文数组的汉明重量数组
#power_std = [2, 0, 2, 4, 2, 0, 3, 4, 4, 2, 3, 2, 2, 1, 2, 3, 2, 3, 2, 2, 2, 2, 3, 1, 1, 1, 3, 2, 1, 1, 1, 2, 3, 3, 2, 2, 0, 2, 2, 1, 2, 2, 3, 4, 2, 2, 3, 3, 2, 3, 2, 1, 4, 3, 3, 1, 3, 2, 1, 2, 2, 3, 3, 2] #第二组明文数组的汉明重量数组
```

#### 完善Corrfun函数

完全根据公式：
$$
x corr=\frac{\sum_n^{}{\left(x_i-\overline{x}  \right)}\cdot \left( y_i-\overline{y} \right)}{\sqrt{\sum_n^{}{\left( x_i-\overline{x} \right) ^2}}\cdot \sqrt{\sum_n^{}{\left( y_i-\overline{y} \right) ^2}}}
$$
推导出的代码。

补充代码如下：

```python
def Corrfun(n, pstd, ptest):  # 计算相关系数
    # 补充相关系数函数，返回两个向量的相关系数值
    pstd_mean = mean(pstd)
    ptest_mean = mean(ptest)
    mole = 0
    denx = 0
    deny = 0
    for i in range(n):
        mole += (ptest[i] - ptest_mean) * (pstd[i] - pstd_mean)
    for i in range(n):
        denx += (ptest[i] - ptest_mean)**2
        deny += (pstd[i] - pstd_mean)**2
    den2 = math.sqrt(denx) * math.sqrt(deny)
    return abs(mole/den2)
```

#### 完善主函数

遍历0-63共64个密钥（密钥的所有可能性），遍历密钥得到的汉明重量数组和正确密钥得到的汉明重量数组共同带入Corrfun函数，计算相关系数，取所有相关系数中的最大值=1对应的密钥，即为正确密钥。

补充代码如下：

```python
if __name__ == "__main__":
    # 遍历所有可能密钥，计算每个猜测密钥对应的相关系数，求最大系数对应的猜测密钥
    keyList = [i for i in range(64)]
    n = len(plainlist)
    Corrlist = []
    for k in range(64):
        hw_std = []
        HWout = sboxout(n, plainlist, k)
        for m in range(n):
            hw_std.append(HWfun(HWout[m]))
        Corrlist.append(Corrfun(n, power_std, hw_std))
    final_dict = dict(zip(keyList, Corrlist))
    print(max(final_dict, key=final_dict.get))
```

#### 程序运行结果
两个长度的数组运行结果都如下
![3-1.2](https://img-blog.csdnimg.cn/img_convert/3e5fb7bae94b76226c8a31b598b724d0.png#pic_center)

<center>
    <b>
        <small> 
            图6.3&nbsp&nbsp&nbsp&nbsp 长度为9&64的明文数组密钥猜测
        </small>
    </b>
</center>

## 思考问题

### 除了利用汉明重量模型，还可以用什么模型进行攻击？

多元模型：汉明重量模型、汉明距离模型

二元模型：比特模型、零值模型

除了汉明重量模型，至少还有三种模型可以使用

实际攻击时采用哪一种仿真模型，需要根据攻击对象（数据）的变化特点、密码芯片对密码算法的实现方式（软件实现、硬件实现）、攻击方法的使用等灵活选择。

比较熟悉的是汉明距离模型，其通常用于比较两个相同长度的二进制字符串。它还可以用于字符串，通过计算不同字符的数量来比较它们之间的相似程度。在硬件上进行CPA攻击时一般采用汉明距离模型，原因之一就是在功耗曲线基数大时，汉明距离模型比汉明重量模型更有优势。

## 总结

在实现CPA仿真实验过程中，我有去测试不同明文长度对密钥猜测结果的影响。如上文，我第一次测试长度为9的明文数组结果是正确的。不过不能确定正确率是100%，所以我循序渐进的进行进一步的测试：

### 阶段实验

**阶段一：**

采用原明文数组`plainlist = [41, 35, 62, 4, 33, 44, 22, 46, 18]`，遍历64个密钥，查看结果。

主函数代码如下：

```python
if __name__ == "__main__":
    keyList = [i for i in range(64)]
    n = len(plainlist)
    for i in range(64):
        power_std = []
        HWout_true = sboxout(n, plainlist, i)  # S盒输出
        for j in range(n):
            power_std.append(HWfun(HWout_true[j]))  # 汉明重量
        Corrlist = []
        for k in range(64):
            hw_std = []
            HWout = sboxout(n, plainlist, k)
            for m in range(n):
                hw_std.append(HWfun(HWout[m]))
            Corrlist.append(Corrfun(n, power_std, hw_std))
        final_dict = dict(zip(keyList, Corrlist))
        print("正确密钥:",max(final_dict, key=final_dict.get))
```

运行部分结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/3f5f1eada9f04fdfb69da8b3d717d88f.png#pic_center)

<center>
    <b>
        <small> 
            图8.1&nbsp&nbsp&nbsp&nbsp 阶段一程序部分输出
        </small>
    </b>
</center>

其余结果相同，说明密钥的更改对正确率没影响。

**阶段二：**

采用长度相同（设长度为19）、明文内容不同的100个数组进行测试，正确密钥设为63。

主函数代码如下：

```python
if __name__ == "__main__":
    for i in range(100):
        plainlist = list(np.random.randint(64, size=19))
        keyList = [i for i in range(64)]
        n = len(plainlist)
        power_std = []
        HWout_true = sboxout(n, plainlist, 63)
        for j in range(n):
            power_std.append(HWfun(HWout_true[j]))
        Corrlist = []
        for k in range(64):
            hw_std = []
            HWout = sboxout(n, plainlist, k)
            for m in range(n):
                hw_std.append(HWfun(HWout[m]))
            Corrlist.append(Corrfun(n, power_std, hw_std))
        final_dict = dict(zip(keyList, Corrlist))
        print("正确密钥:",max(final_dict, key=final_dict.get))
```

运行部分结果如下：

![3-1.4](https://img-blog.csdnimg.cn/img_convert/257c17aae79fae9465778e6bd0186bef.png#pic_center)
<center>
    <b>
        <small> 
            图8.2&nbsp&nbsp&nbsp&nbsp 阶段二程序部分输出
        </small>
    </b>
</center>
其余结果相同，说明明文内容的更改对正确率没影响。

**阶段三：**

更改明文长度，从9—64，DES的明文分组上限就是64，至于为什么从9开始，是因为长度过小时可能会出现Corrfun函数内，被除数为0的情况。正确密钥设为45。

主函数代码如下：

```python
if __name__ == "__main__":
    for i in range(9,65):
        plainlist = list(np.random.randint(64, size = i))
        keyList = [i for i in range(64)]
        n = len(plainlist)
        power_std = []
        HWout_true = sboxout(n, plainlist, 45)
        for j in range(n):
            power_std.append(HWfun(HWout_true[j]))
        Corrlist = []
        for k in range(64):
            hw_std = []
            HWout = sboxout(n, plainlist, k)
            for m in range(n):
                hw_std.append(HWfun(HWout[m]))
            Corrlist.append(Corrfun(n, power_std, hw_std))
        final_dict = dict(zip(keyList, Corrlist))
        print("正确密钥:",max(final_dict, key=final_dict.get))
```

运行部分结果如下：

![3-1.5](https://img-blog.csdnimg.cn/img_convert/e5b4924bd69342de77ba05c5e16eb6b1.png#pic_center)
<center>
    <b>
        <small> 
            图8.3&nbsp&nbsp&nbsp&nbsp 阶段三程序部分输出
        </small>
    </b>
</center>
其余结果相同，说明明文数组长度的更改对正确率没影响。

### 综合分析

阶段实验证明CPA仿真攻击的正确率不管在什么情况下都是100%，比DPA效果好多了。原因就在于CPA考虑了方差问题，使得其猜测精度有很明显的提高。

