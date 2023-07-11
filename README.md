#### 以上两个文件为IIC通信协议的c8t6为主控的mpu6050解算程序

#####      第一个文件使用了卡尔曼滤波姿态解算的方式融合了加速度计和陀螺仪积分，mpu6050自身受到重力的作用有向下的加速度g。

######               第一点是通过加速度计的方式进行解算

![image-20230711122715158](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711122715158.png)



​        ![image-20230711122734582](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711122734582.png)

​     可以得出结论，yaw轴无法由重力加速度得出，只能由陀螺仪的速度积分得出。

######               第二点是通过陀螺仪进行积分的方式进行姿态解算。  

######              ![image-20230711123243168](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711123243168.png)             

![image-20230711123356762](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711123356762.png)      

  由mpu6050的陀螺仪只能得到相对于mpu6050坐标系下的速度矢量，其转移到绝对坐标系下的矩阵变换如上图所示。

######         卡尔曼滤波进行处理(本质上就是一个最小协方差的估计处理)

- 第一步先验估计

  ![image-20230711123840624](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711123840624.png)

​       这里的是根据控制方程得出的先验估计值，其中前面的x_k_hat值为pitch角和roll角，A矩阵和B矩阵相应可以列出矩阵表达式。

- 第二步为计算先验误差协方差矩阵，这个p_k_hat是x_k真实值与x_k_hat的一个误差矩阵，详细可关注DR_CAN

  ​                                                                     ![image-20230711124247248](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711124247248.png)

​       这是线性卡尔曼滤波方程才能列出得到误差协方差表达式，Q矩阵是预先可以设定好的一个值，一般为{[0.0.25],[0.0.25]}

-   第三步为计算卡尔曼增益

  ​                                                                    ![image-20230711124849280](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711124849280.png)

-    第四步为进行最优估计

  ​                                                                   ​![image-20230711124925534](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711124925534.png)  

- 第五步为更新协方差矩阵

  ​                                                                    ![image-20230711124959457](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711124959457.png)

#####   第二个文件是通过官方自带的d_m_p库进行移植得到的四元数解算而得的旋转向量

![image-20230711125406711](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711125406711.png)

四元数基本概念，四元数是通过复数的方式进行二维平面的旋转。

可将四元数转换为旋转矩阵的方式

![image-20230711125954729](C:\Users\hua\AppData\Roaming\Typora\typora-user-images\image-20230711125954729.png)
