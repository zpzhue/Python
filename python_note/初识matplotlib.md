Python中的可视化库包括：Pandas、Matplotlib、Seaborn，其中最核心的是Matplotlib。

Matplotlib 的基本图包含以下类型：

- boxplot

- violinplot

- polarplot

- hexbin

- histogram

风格设置的切换：可以设置成ggplot的风格，也可以设置成黑底背景的风格，还可以设置成一般财务公司常用的风格，如excel风格。

Python可视化系列将分块讲解 matplotlib 库里的绘图方法。

本期讲解第一部分：箱线图、直方图、小提琴图、雷达图在Python中的实现。

##1 基本图 part 1 —— boxplot、violinplot、histogram、polar##

    import matplotlib.pyplot as plt
	import numpy as np

如果你使用 jupyter notebook 环境，想要使图形内嵌地显示在 jupyter notebook 中，而不是弹出一个绘图窗口，需要使用一些 magic，如下：

    %matplotlib inline

但这样设置后，也存在一个缺陷：除非将所有绘图代码一次全部执行，否则，无法叠加绘图。

###1.1 boxplot###

####1.1.1 生成随机数####

在这里我们利用 numpy 库生成三组正态分布随机数，为了使结果可重现，我们还需要播撒随机种子,不妨设定随机种子编码为 123：

    np.random.seed(123)
	all_data=[np.random.normal(0,std,100) for std in range(1,4)].1.

####1.1.2 画板 + 轴 + 描点上色 = 图####

早有《黑客与画家》一书论述过编程与绘画的相似之处，在用 Python 的 matplotlib 作图时，和真实绘图方式一致，我们先要有:

- 画板（figure）

然后确定:

- 绘图框架结构（axis）

最后再基于框架结构:

- 描点上色（输入data，指定上色参数patch_artist=True）

非常容易上手！以上三个步骤只需要用三条命令即可完成，下面我们就来试试：
	
	figure,axes=plt.subplots() #得到画板、轴
	axes.boxplot(all_data,patch_artist=True) #描点上色
	plt.show() #展示


plt.subplots 顾名思义是子图集合（用过 matlab 的童鞋想必已经想起来些什么了），当不指定 plt.subplots() 的 nrows 和 ncols 参数值时，默认只生成一张图。

Note：默认 patch_artist=False，所以我们需要指定其参数值为 True，即可自动填充颜色。

####1.1.3 多图输出####

有时我们会有把多张图放在同一行输出的对比需求，这时只需指定 plt.subplots() 函数的 nrows，ncols 参数值，举个例子，我想生成一个 2x3 的面板，即每一行放三张图，只需设定 nrows=2，ncols=3。

当然，你还可以通过参数 figsize 指定图形大小。

下面我们将两张图放在一行输出试试：

	fig,axes=plt.subplots(nrows=1,ncols=2,figsize=(9,4))
	#首先有图（fig），然后有轴（ax）
	bplot1=axes[0].boxplot(all_data,
	                       vert=True,
	                       patch_artist=True)
	#axes[0]表示在第一张图的轴上描点画图
	#vert=True表示boxplot图是竖着放的
	#patch_artist=True 表示填充颜色
	
	bplot2 = axes[1].boxplot(all_data,
	                         notch=True,  # notch shape
	                         vert=True,   # vertical box aligmnent
	                         patch_artist=True)   # fill with color
	plt.show()

第一张图是矩形箱线图，第二张图是 notch 形状的箱线图，显然，把两张图放在同一行输出非常有利于对比观察图形差异。

####1.1.4 美化图形####

基本图形画得差不多了，接下来就只需要让她美美美了！

我们先来查看一下我们之前作出的箱线图对象的类型：

	type(bplot1) #返回字典
	#dict

噢！它是字典类型呀！

- 颜色填充

颜值即正义！要选个好的配色不容易呢…

填充颜色基本遵循以下步骤：

1. 指定箱线图中每个箱子的填充颜色，如：red、green、blue、pink 等

2. 构造字典存放箱子配色 —— {箱线图中每个箱子patch：对应的配色color}

3. 重新上色 —— patch.set_facecolor(color)

好啦，把代码摆出来，一目了然！
	
	#颜色填充
	colors = ['pink', 'lightblue', 'lightgreen']
	for bplot in (bplot1, bplot2):
	    for patch, color in zip(bplot['boxes'], colors):
	        patch.set_facecolor(color)

添加网格线
你可以：

1. 指定在x轴上添加网格线（ax.xaxis.grid(True)），或者在y轴上添加网格线（ax.yaxis.grid(True)）

2. 指定 x 轴和 y 轴上的刻度个数（ax.set_xticks([1,2,3]),ax.set_yticks([1,2,3,4,5,6,7,8])）

3. 设置 x 轴名称（ax.set_xlabel("xlabel")）

4. 设置 y 轴名称（ax.set_xlabel("ylabel")）



	# 加水平网格线
	for ax in axes:
	    ax.yaxis.grid(True) #在y轴上添加网格线
	    ax.set_xticks([y+1 for y in range(len(all_data))] ) #指定x轴的轴刻度个数
	    ## [y+1 for y in range(len(all_data))]运行结果是[1,2,3]
	    ax.set_xlabel('xlabel') #设置x轴名称
	    ax.set_ylabel('ylabel') #设置y轴名称


-

- 添加刻度

添加刻度名称，我们需要使用 plt.setp() 函数：

	# 加刻度名称
	plt.setp(axes, xticks=[1,2,3],
	         xticklabels=['x1', 'x2', 'x3'])

我们需要告诉 plt.setp，我们的刻度数是哪些，以及我们想要它添加的刻度标签是什么。

讲解完代码，让我们把代码汇总，画个图看看：

- 添加标题

使用 ax.set_title 来设置单个图形主标题，用 fig.suptitle 来设置整张画板的标题：

	np.random.seed(1221)
	data=np.random.binomial(n=10,p=0.76,size=(10,3))
	labels=list("ABC")
	fig,ax=plt.subplots(figsize=(9,6))
	ax.boxplot(data,labels=labels,patch_artist=True)
	plt.rcParams['font.sans-serif']=['SimHei'] #正确显示中文
	plt.rcParams['axes.unicode_minus']=False #用来正确显示负号
	ax.set_title("设置 fontsize=15 的标题",fontsize=15) #设置字体大小为18号字体
	fig.suptitle("Boxplot Examples",fontsize=24)
	plt.show()

想必细心的朋友已经发现了我在代码里偷偷加了**两行设置字体**的代码，这两行代码专为**防止出现中文乱**码而生！

`plt.rcParams[‘font.sans-serif’]=[‘SimHei’]` -> 用来正确显示中文
`plt.rcParams[‘axes.unicode_minus’]=False` -> 用来正确显示负号

- 其他参数

在图上标记出均值线

	fig,axes=plt.subplots(1,2,figsize=(9,5))
	axes[0].boxplot(data,labels=labels,showmeans=True,meanline=False)
	axes[0].set_title("默认 meanline=False",fontsize=15)
	axes[1].boxplot(data,labels=labels,showmeans=True,meanline=True)
	axes[1].set_title("meanline=True",fontsize=15)
	plt.show()

`meanline=False`，那么均值位置会在图中用小三角表示出来

`meanline=True`，那么均值位置会在图中用虚线表示出来

**在图上去除离群值**
	
	fig,axes=plt.subplots(1,2,figsize=(9,5))
	axes[0].boxplot(data,labels=labels,patch_artist=True)
	axes[0].set_title("默认 showfliers=True",fontsize=15)
	axes[1].boxplot(data,labels=labels,patch_artist=True,showfliers=False)
	axes[1].set_title("showfliers=False",fontsize=15)
	plt.show()
	默认 showfliers=True，那么图中会显示出离群值
	
	showfliers=False，那么图中会去除离群值

###1.2 violinplot###

- 小提琴图

小提琴图其实是箱线图与核密度图的结合。箱线图展示了分位数的位置，小提琴图还展示了任意位置的密度，通过小提琴图可以知道哪些位置的密度较高。

	import random
	import numpy as np
	import matplotlib.pyplot as plt
	
	fs=10
	pos = [2,4,6]
	data=[np.random.normal(0,std,size=150) for std in pos]
	
	fig,axes = plt.subplots(nrows=1,ncols=2,figsize=(9,6))
	
	axes[0].violinplot(data,pos,points=20,widths=0.7,
	                    showmeans=False,showextrema=True,showmedians=True)
	axes[1].violinplot(data, pos, points=80, vert=False, widths=0.7,
	                      showmeans=True, showextrema=True, showmedians=True)
	plt.show()
在左上图中，每个小提琴图中间那条横线是中位数，小提琴上下两条横线分别表示最大值、最小值。外部扩张的区域即为核密度估计（在概率论中用来估计未知的密度函数，属于非参数检验方法之一），表现了这些位置的数据密度。

**Note**：标题、字体、颜色的设置同 boxplot 部分。

###1.3 histogram###

####1.3.1 等距直方图####

想必大家已经很熟悉直方图了，下面我们来实现一下直方图的绘制：

- 生成随机数

生成一组服从 mu=100，sigma=15 的正态分布随机数：

	import matplotlib.mlab as mlab
		
	np.random.seed(1)
	mu = 100  # mean of distribution
	sigma = 15  # standard deviation of distribution
	x = mu + sigma * np.random.randn(437)

- 指定分组个数

	num_bins = 10
上面的代码指定了直方图的组数为 10

- 画出直方图

通过调用 as.hist 函数，来生成组数为 10 的直方图：

	fig, ax = plt.subplots()
	n, bins_limits, patches = ax.hist(x, num_bins, normed=1)
	print("n 是分组区间对应的频率：",n,end="\n\n")
	print("bins_limits 是分组时的分隔值：",bins_limits,end="\n\n")
	print("patches 指的是是直方图中那10个矩形对象",end="\n\n")
	
	#n 是分组区间对应的频率： [ 0.00157167  0.00419112  0.00890613  #0.01807421  0.02802812  0.02462284
	#  0.01833616  0.00602474  0.00392918  0.00078584]
	
	#bins_limits 是分组时的分隔值： [  58.103725     66.83963817   #75.57555134   84.3114645    93.04737767
	#  101.78329084  110.51920401  119.25511718  127.99103035  #136.72694352
	#  145.46285669]
	
	#patches 指的是是直方图中那10个矩形对象

- 添加经验分布曲线

.

	fig, ax = plt.subplots()
	n, bins_limits, patches = ax.hist(x, num_bins, normed=1)
	# add a 'best fit' line
	y = mlab.normpdf(bins_limits, mu, sigma)
	ax.plot(bins_limits, y, '--')
	ax.set_xlabel('Smarts')
	ax.set_ylabel('Probability density')
	ax.set_title(r'Histogram of IQ: $\mu=100$, $\sigma=15$')
	plt.show()

####1.3.2 不等距分组####

上面的直方图都是等距的，但有时我们需要得到不等距的直方图，这个时候只需要确定分组上下限，并指定 histtype="bar" 就可以了：

	fig, ax = plt.subplots()
	bins = [50, 60, 70, 90, 100,110, 140, 150]
	ax.hist(x, bins, normed=1, histtype='bar', color="g",rwidth=0.8)
	ax.set_title('不等距分组')
	plt.show()

####1.3.3 多类型直方图####

	fig,ax=plt.subplots()
	x_multi = [np.random.randn(n) for n in [10000, 5000, 2000]]
	n_bins=10
	ax.hist(x_multi, n_bins, histtype='bar',label=list("ABC"))
	ax.set_title('MultiType data')
	ax.legend(prop={'size': 10})
	fig.tight_layout()
	plt.show()

实际绘图代码与单类型直方图差异不大，只是增加了一个图例项

- 添加图例

.

1. 在 ax.hist 函数中先指定图例 label 名称

2. 通过 ax.legend 函数来添加图例

###1.4 雷达图（Polar）###

	# 构造随机数
	N = 150
	r = 2 * np.random.rand(N)
	theta = 2 * np.pi * np.random.rand(N) 
	area = 20 * r**2 # 指定气泡面积大小
	colors = theta #指定气泡颜色，可以是一个数值序列
	
	ax = plt.subplot(polar=True)
	c = ax.scatter(theta, r, c=colors, s=area,cmap='hsv', alpha=0.75)
	
	plt.show()
细心的童鞋肯定已经发现，在这里我们使用的函数是 plt.subplot，而不是 plt.subplots。这两个函数的区别是：

函数区别plt.subplotplt.subplots1ax=~，只能返回一个轴，该轴是指定图的对应轴fig,ax=~，可返回多张图及其对应轴2可设定polar=T，建立雷达图画板没有polar参数3可以设定projection方式没有projection参数4如果nrows,ncols小于10，可以使用缩写不可以缩写

**Note：**

这里的 ~ 表示各自对应的函数

“缩写”是指比如 nrows=2，ncols=3，要对按行数的第三张图进行绘图填充，可以简写为 233