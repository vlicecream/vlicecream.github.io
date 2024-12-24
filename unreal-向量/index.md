# 向量


# ***标量与向量***

## ***标量***

*标量有大小 可以是正负，但是他没有方向*

## ***向量***

*向量有大小，并且有方向*

# ***2D向量***

## ***向量的加法***

1. *介绍*
   
   *其实向量的加法可以利用平行四边形，如图所示*

2. *图示*
   
   

3. *代码*
   
   ```cpp
   struct f_vector_2d
   {
       f_vector_2d(const float in_x, const float in_y);
   
       float x;
       float y;
   
       f_vector_2d operator+ (const f_vector_2d& a) const
       {
           return f_vector_2d(a.x + x, a.y + y);y + a.y);
       }
   };
   
   f_vector_2d::f_vector_2d(const float in_x, const float in_y)
       : x(in_x), y(in_y)
   {
   }
   ```

## 向量的减法

1. *介绍*

2. *图示*

3. *代码*
   
   ```cpp
       f_vector_2d operator- (const f_vector_2d& a) const
       {
           return f_vector_2d(a.x - x, a.y - y);
       }
   ```

## ***向量的乘法***

1. *介绍*
   
   *向量和标量不可以相加，但是可以相乘*

2. *图示*

3. *代码*

