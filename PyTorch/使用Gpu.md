对模型使用 cuda()方法，对损失函数使用cuda()方法并赋值
Dataloader取出的数据也要使用cuda()方法进行赋值

首先要判断 torch.cuda.is_available()


也可以设置device，然后对上面这些使用to()函数