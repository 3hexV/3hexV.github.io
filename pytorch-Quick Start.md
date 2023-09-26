1. 查看pytorch版本
   ```
   import torch
   torch.__version__
   ```
2. 基本使用
   1. 创建矩阵
      ```python
      # 创建一个未初始化值填满的tensor
      x = torch.empty(3, 4)
      torch.empty_like(x) # empty方式创建一个x同维度的tensor

      # 创建一个单位矩阵
      x = torch.eye(4)

      # 创建一个对角矩阵
      t = torch.tensor([1, 2, 3, 4])
      x = torch.diag(t)

      # 创建一个随机数值填满的tensor,0-1均匀分布
      # x = torch.randn(3, 4) 0-1正态分布
      x = torch.rand(3, 4)
      torch.rand_like(x) # rand方式创建一个x同维度的tensor

      # 创建一个(0-8)的随机整数的tensor，最小值默认为0
      x = torch.randint(8, (3, 4))
      x = torch.randint_like(x, 7) # randint(0-7)方式创建一个x同维度的tensor

      # 创建服从均值为2，标准差为3的tensor
      x = torch.normal(2, 3, (5, 6))

      # 创建一个一维张量
      x = torch.arange(5) # 一维张量 [1, 2, 3, 4]
      x = torch.arange(0, 2, 0.5) # 初始值为0，最大值为2，步长为0.5的张量 [0.0000, 0.5000, 1.0000, 1.5000]
      x = torch.linspace(1, 5, 4) # 初始值为1，最大值为5，从中等距取出4个数的张量 [1.0000, 2.3333, 3.6667, 5.0000]

      # 创建一个0填满的tensor
      x = torch.zeros(3, 4)
      torch.zeros_like(x) # zeros方式创建一个x同维度的tensor

      # 创建一个全为1的，dtype类型的，分配在gpu上的一个tensonf
      x = torch.ones((2, 3), device='cuda:0')
      x = torch.ones_like(x) # ones方式创建一个x同维度的tensor

      # 创建一个值全为9的tensor
      x = torch.full((4, 5), 9)
      x = torch.full_like(x, 7) # full方式创建一个x同维度的tensor 

      # 由一个tensor创建一个全为1，类型为dtype的tensonf，new_ones可以指定分配到GUP上
      y = x.new_ones((5, 6), dtype=torch.int64)
      
      # 根据传入参数创建一个tensor
      torch.tensor([3, 4.5, 9])
      tensor([3.0000, 4.5000, 9.0000])
      # 指定类型
      x = torch.tensor([3, 4, 9], dtype=torch.float)
      tensor([3., 4., 9.])

      # 获得tensor的维度
      x.size()
      ```
   2. 基本运算
      ```python
      x = torch.randint(0, 9, (4, 4))
      y = torch.diag(torch.tensor([1, 2, 3, 4]))

      # 加法
      z = x + y
      z = tensor.add(x, y)

      # 减法
      z = x - y
      z = tensor.sub(x, y)
      ```
   3. 索引
      ```python
      x = torch.randint(0, 9, (4, 4, 5, 5))
      # :表示选择当前维度全部索引, 3:表示选择当前维度索引3到最后一个索引的之间全部
      # :3表示选择当前维度的第一个索引维到第3个索引之间全部
      # 1表示选择当前维度的第1个索引
      x[:, :, :, 1] 

      # view来改变维度
      y = x.view(-1) # 将x压缩到一维，一维长度写-1表示由代码自动计算
      y = x.view(5, -1) # 表示将x压缩到二维，第一维度长度为5，第二维度长度代码自动计算
      ```
   4. numpy操作
      ```python
      x = torch.randint(0, 9, (4, 2))

      # tensor转numpy
      x.numpy()

      # numpy转tensor
      x = numpy.ones((2, 3))
      y = torch.from_numpy(x)
      ```
   5. 反向传播
      ```python
      x = torch.randn((3, 4), requires_grad=True)
      ```