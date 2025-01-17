Unity版本：Unity 2022.3.17f1c1。
***
坐标空间分类：
- 纹理空间（UV坐标）UV：float2；
  - U为横坐标，V为纵坐标；
  - 范围[0, 1]。
- 切线空间（表面贴图空间）TS：float3；
  - 顶点上存储模型空间法线向量和切线向量，转换为世界空间法线向量和切线向量；
  - 世界空间法线向量和切线向量，通过叉乘计算世界空间副切线向量；
  - 由世界空间，切线向量u，副切线向量v，法线向量n，构建从切线空间到世界空间转换矩阵；
  - 法线贴图采样到数据，将其从切线空间转换为世界空间，再用于光照计算。
- 模型空间：OS：float4；
  - 本地坐标：相对于模型锚点坐标。   
- 世界空间：WS：float4；
  - 世界坐标：相对于世界坐标原点坐标。   
- 视图空间：VS：float4；
  - 视图坐标：相对于摄像机锚点坐标。 
- 齐次裁剪空间：CS：float4；
  - 视锥体裁剪空间下坐标；
  - 范围：
    - x[-w , w]；
    - y[-w , w]；
    - z[-w, w]；
    - w[Near, Far]。
  - 判断坐标是否在视锥体外部，-w<= x/y/z <= w 的话则点在视锥体内部。 
- 归一化设备坐标：NDC：float4；
  - 用齐次坐标系的w分量去除以x、y、z分量。
  - 范围：
    - x[-1 , 1]；
    - y[-1 , 1]；
    - z[-w, w]：posNDC.z == posCS.z；
    - w[Near, Far]：posNDC.w == posCS.w。  
- 屏幕空间：SS：float2；
  - 屏幕X轴和Y轴坐标；
  - 范围：
    - x[0, ScreenPixelWidth]；
    - y[0, ScreenPixelWidth]；  
- 视口空间：VP：float2。
  - 归一化后屏幕X轴和Y轴坐标；
  - 范围：
    - x[0, 1]；
    - y[0, 1]。 
参考资料：

https://zhuanlan.zhihu.com/p/447671623

https://zhuanlan.zhihu.com/p/505030222?utm_id=0&wd=&eqid=8158199c0001fd4300000004646d6970
***
组件实现：
- 位置转换：PositionTransformation
- 缩放转换：ScaleTransformation
- 旋转转换：RotationTransformation
***
旋转转换基础：以Z轴旋转为例。
- Unity使用左手坐标系：Z轴将逆时针旋转。

  ![image](https://github.com/user-attachments/assets/4f45bef6-96b3-42fd-b4e0-09fdda82cda3)

- （1,0）和（0,1）对应0度，90度和180度。

   ![image](https://github.com/user-attachments/assets/2021138f-5ec5-4e78-8b23-b3c0fcf7425a)

- 从（1,0）开始，正弦波与Y坐标匹配，余弦波与X坐标匹配：

  ![image](https://github.com/user-attachments/assets/a7931c6b-44e5-458e-8f31-1a77a72b5ffe)

- Z轴旋转时，坐标z不变，x和y坐标发生相应变化：
  - (x, y)；
  - x(1, 0) + y(0, 1)；
  - x(cosZ, sizeZ) + y(-sinZ, cosZ)；
  - (xcosZ-ysinZ, xsinZ+ycosZ)。
- 对应矩阵转换：

  ![image](https://github.com/user-attachments/assets/7167b353-d20a-406c-ab4a-f897c1d376f7)

***
矩阵基础：两个 2×2 矩阵相乘。

![image](https://github.com/user-attachments/assets/4b43c5fc-4cb2-404a-be8b-c0f51cabb7fa)

***
转换矩阵：
- 位置转换矩阵：

  ![image](https://github.com/user-attachments/assets/9c7d3f89-865f-4d5a-9670-0961e4f0e3b9)

  ![image](https://github.com/user-attachments/assets/b2676aff-651a-4a2b-a0b3-ef1cde906fad)

- 缩放转换矩阵：

  ![image](https://github.com/user-attachments/assets/1d7410c4-a674-4cbb-b66c-61aae29b3ee9)
  
- 旋转转换矩阵：
  - Z轴旋转转换矩阵：

    ![image](https://github.com/user-attachments/assets/68d2c70c-b04d-454a-b4ae-b9bb559cc790)

  - Y轴旋转转换矩阵：

    ![image](https://github.com/user-attachments/assets/e17126f5-0d90-4528-b8ad-e41f239e48d5)

  - X轴旋转转换矩阵：

    ![image](https://github.com/user-attachments/assets/84c16d60-d786-4365-aeee-63bf2b2e24ab)

  -  X*(Y*Z)旋转转换矩阵：

     ![image](https://github.com/user-attachments/assets/d00131e9-13ba-497f-9f1f-0f22e964af37)

参考资料：https://catlikecoding.com/unity/tutorials/rendering/part-1/


