## 朴素贝叶斯分类实例：检测SNS社区中不真实账号

      下面讨论一个使用朴素贝叶斯分类解决实际问题的例子，为了简单起见，对例子中的数据做了适当的简化。

      这个问题是这样的，对于SNS社区来说，不真实账号（使用虚假身份或用户的小号）是一个普遍存在的问题，作为SNS社区的运营商，希望可以检测出这些不真实账号，从而在一些运营分析报告中避免这些账号的干扰，亦可以加强对SNS社区的了解与监管。

      如果通过纯人工检测，需要耗费大量的人力，效率也十分低下，如能引入自动检测机制，必将大大提升工作效率。这个问题说白了，就是要将社区中所有账号在真实账号和不真实账号两个类别上进行分类，下面我们一步一步实现这个过程。

      首先设C=0表示真实账号，C=1表示不真实账号。

_1、确定特征属性及划分_

      这一步要找出可以帮助我们区分真实账号与不真实账号的特征属性，在实际应用中，特征属性的数量是很多的，划分也会比较细致，但这里为了简单起见，我们用少量的特征属性以及较粗的划分，并对数据做了修改。

      我们选择三个特征属性：a1：日志数量/注册天数，a2：好友数量/注册天数，a3：是否使用真实头像。在SNS社区中这三项都是可以直接从数据库里得到或计算出来的。

      下面给出划分：a1：{a&lt;=0.05, 0.05&lt;a&lt;0.2, a&gt;=0.2}，a1：{a&lt;=0.1, 0.1&lt;a&lt;0.8, a&gt;=0.8}，a3：{a=0（不是）,a=1（是）}。

_2、获取训练样本_

      这里使用运维人员曾经人工检测过的1万个账号作为训练样本。

_3、计算训练样本中每个类别的频率_

      用训练样本中真实账号和不真实账号数量分别除以一万，得到：

![](http://latex.codecogs.com/gif.latex?P%28C=0%29=8900/100000=0.89)

![](http://latex.codecogs.com/gif.latex?P%28C=1%29=110/100000=0.11)

_4、计算每个类别条件下各个特征属性划分的频率_

![](http://latex.codecogs.com/gif.latex?P%28a_1%3C=0.05%7CC=0%29=0.3)

![](http://latex.codecogs.com/gif.latex?P%280.05%3Ca_1%3C0.2%7CC=0%29=0.5)

![](http://latex.codecogs.com/gif.latex?P%28a_1%3E0.2%7CC=0%29=0.2)

![](http://latex.codecogs.com/gif.latex?P%28a_1%3C=0.05%7CC=1%29=0.8)

![](http://latex.codecogs.com/gif.latex?P%280.05%3Ca_1%3C0.2%7CC=1%29=0.1)

![](http://latex.codecogs.com/gif.latex?P%28a_1%3E0.2%7CC=1%29=0.1)

![](http://latex.codecogs.com/gif.latex?P%28a_2%3C=0.1%7CC=0%29=0.1)

![](http://latex.codecogs.com/gif.latex?P%280.1%3Ca_2%3C0.8%7CC=0%29=0.7)

![](http://latex.codecogs.com/gif.latex?P%28a_2%3E0.8%7CC=0%29=0.2)

![](http://latex.codecogs.com/gif.latex?P%28a_2%3C=0.1%7CC=1%29=0.7)

![](http://latex.codecogs.com/gif.latex?P%280.1%3Ca_2%3C0.8%7CC=1%29=0.2)

![](http://latex.codecogs.com/gif.latex?P%28a_2%3E0.2%7CC=1%29=0.1)

![](http://latex.codecogs.com/gif.latex?P%28a_3=0%7CC=0%29=0.2)

![](http://latex.codecogs.com/gif.latex?P%28a_3=1%7CC=0%29=0.8)

![](http://latex.codecogs.com/gif.latex?P%28a_3=0%7CC=1%29=0.9)

![](http://latex.codecogs.com/gif.latex?P%28a_3=1%7CC=1%29=0.1)

_5、使用分类器进行鉴别_

      下面我们使用上面训练得到的分类器鉴别一个账号，这个账号使用非真实头像，日志数量与注册天数的比率为0.1，好友数与注册天数的比率为0.2。

![](http://latex.codecogs.com/gif.latex?P%28C=0%29P%28x%7CC=0%29=P%28C=0%29P%280.05%3Ca_1%3C0.2%7CC=0%29P%280.1%3Ca_2%3C0.8%7CC=0%29P%28a_3=0%7CC=0%29=0.89*0.5*0.7*0.2=0.0623)

![](http://latex.codecogs.com/gif.latex?P%28C=1%29P%28x%7CC=1%29=P%28C=1%29P%280.05%3Ca_1%3C0.2%7CC=1%29P%280.1%3Ca_2%3C0.8%7CC=1%29P%28a_3=0%7CC=1%29=0.11*0.1*0.2*0.9=0.00198)

      可以看到，虽然这个用户没有使用真实头像，但是通过分类器的鉴别，更倾向于将此账号归入真实账号类别。这个例子也展示了当特征属性充分多时，朴素贝叶斯分类对个别属性的抗干扰性。
