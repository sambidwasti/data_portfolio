---
title: "Principle Component Analysis (PCA)"
date: 2022-06-25T18:35:46+05:30
draft: false
description: "Quick example of PCA"
layout: "gallery"
tags: pca, python, projects
---

# Overview:
I noticed randomm articles that try to explain PCA and use python packages that has PCA to look at data. Most of the time, it is refrenced as dimension reduction but it does not address the beauty behind the mathematics of this process. I have attempted to document a simple example here, which i have also explained in a section in my [Phd. dessertation](https://arxiv.org/abs/2012.12939).

# Principle Component Analysis (PCA)
PCA is an powerful tool that uses eigen vectors and eigen values to find the correlated variables and collapse them to reduce the dimensions. This is better explained by various steps.

## 1. Initial data.
Let us assume a dataset, that is randomly peturbed along y=x and multipled by a constant which showcases the power of PCA in correlated dataset. And lets assume that we have a z variable that depends on some combination of x and y. 

   
|x | y| z |
|-|-|-|    
|2.50|24.0|0.40|    
|0.50|7.00|0.69|    
|2.20|29.00|1.92|    
|1.90|22.00|0.75|    
|3.10|30.00|7.48|    
|2.30|27.00|4.84|    
|2.00|16.00|0.06|    
|1.00|11.00|9.20|    
|1.50|16.00|5.28|    
|1.10|9.00|8.79|     

<img src="https://drive.google.com/uc?id=1wGKFbk-d_Z1UqXTD1EcxLOlV4JUM-ML9"
     alt="sample image"
     style="display: block; margin-right: auto; margin-left: auto; width: 90%;
     box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19)" />


## 2. Standardize the data.
This is an important step to normalize the data. Another way to think about it is that, this removes the units from the data as they are normalized or centered at 0(or its mean) and amount of std from 0. This is done by standardizing the data which includes substracting the mean and dividing by the standard deviation.

|Sx | Sy|
|-|-|  
|0.9262 |0.6101|
|-1.7585|-1.5067| 
|0.5235 |1.2328| 
|0.1208 |0.3611|
|1.7317 |1.3573|  
|0.6577 |0.9837|    
|0.2551 |-0.3861|   
|-1.0874|-1.0086|    
|-0.4162|-3.8602|   
|-0.9531|-1.2577|

As we can see below, we only scaled the axis but the feature of the data is the same.

using the sklearn, we can use python to achieve this. 

```python
from sklearn.preprocessing import StandardScaler
data = np.column_stack((x, y)) # Assuming we have X and Y np.array
scaler = StandardScaler()
data_scaled = scaler.fit_transform(data)
```

This results in data_scaled as standardized variable for the original data.

<img src="https://drive.google.com/uc?id=1xWi7SCIYK-fWBQeJ8W8hGT3UKhVOzLKQ"
     alt="sample image"
     style="display: block; margin-right: auto; margin-left: auto; width: 90%;
     box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19)" />

## Correlation/Covariance Matrix.
We can then get the covariance/correlation matrix. 
This is basically, a 2x2 matrix where we have xx,xy,yx,yy correlations. The xx value is determined by multiplying, ith x component with ith x component, and then summing all of them and dividing it by n (no. of i elements). This should be 1 for xx and yy column. The xy or yx is done by multiplying ith x with ith y and summing them. The correlation or standardized covariance should be same but looks like there is a differnce in formula where the division for covariance is done by n-1 instead of n. If the dataset is large, the difference between n and n-1 is very little. 

The correlation matrix thus comes out to be:
```
  1.0 0.926
0.926 1.0
```

The 0.926 value means that x and y are highly correlated. The positive sign means that its positively correalatd.

## Eigen values and Eigen vectors.

The eigen values and eigen vectors helps to understand the variation in the data. 
We can get the eigen values and vectors via 

```
eigenvalues, eigenvectors = np.linalg.eig(cor_mat)
```

which results in eigen values as: 1.926, 0.074 and the corresponding eigen vectors are:

``` 
[0.70710678, -0.70710678] 
[0.70710678,  0.70710678]
``` 
respectively. First column is the eigen vector for 1st value. (0.707, 0.707) represents the 1.926 eigen value. 

The eigen values and vectors define the data:
* The eigen values define the variation present in the data. 
* Here we see that the first eigen values covers 1.926 amount and second 0.074.       
* The values itself doesnt make much sense so we can use a % value out of the total 100% (1.926 + 0.074) of 2. 
* So the first eigen vector (1.926) covers 96.2% of the data and the second only 3.8%.
* The eigen vectors associated with them define the data and covers the variation accordingly. 
* Each eigen vectors are orthogonal to each other.

Visually, what it means is shown below where blue line is the eigen vector1 and green is the eigen vector 2. (Note: Usually this is defined by an arrow.)


<img src="https://drive.google.com/uc?id=13kODws-xuDRtWZA_uIjzBiX5jwnYYb1L"
     alt="sample image"
     style="display: block; margin-right: auto; margin-left: auto; width: 90%;
     box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19)" />

## Principle Components.

In the above picture, the co-ordinate axis is defined by SX and SY. However, we can represent it by the axis along eigen vectors. But the eigen vectors are just a vector, we need to know the transformed co-ordinates (new variables) that takes it from (SX,SY) to (eig1,eig2). The Principle Components are the new variables that are along the eigenvalue coordinates.

This rotation is done simply by:    
PC1_i = SX_i * Eig1[0] + SY_i * Eig1[1]     
PC2_i = SX_i * Eig2[0] + SY_i * Eig2[1]     

|PC1 | PC2|
|-|-|  
|1.0864 |-0.2235|
|-2.3089|0.1781| 
|1.2419 |0.5015| 
|0.3407 |0.1699|
|2.1843 |-0.2647|  
|1.1607 |0.2305|    
|-0.0926 |-0.4533|   
|-1.4821|0.0557|    
|-0.5672|0.02130|   
|-1.5633|-0.2153|     

>Note: We get exactly the same no. of PCs as the beginning variables. We had 2 starting variables, x and y so we have 2 PCs. 

Now lets see the data in the PC1 and PC2 space which is the same data just rotated.

<img src="https://drive.google.com/uc?id=1iVRJtg9LI76TRfHaosIpMVBXqNgq1GDM"
     alt="sample image"
     style="display: block; margin-right: auto; margin-left: auto; width: 90%;
     box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19)" />



## Collapsing the dimension.

The most important concept of the PCA is to reducing the no. of dependent variables. This can be thought of also as removing the noise.          
       
Here, since most of the variation is defined by the 1st eigen value, and the first eigen vectors and hence the 1st PC, we can just represent the data with just one variable PC1 and collapse the PC2 dimension and we have the data defined by 1 variable PC1 instead of 2 variables. One thing to note is that, PC1 still consists of x and y, but is a linear combination of those values. 

The resulting plot will be as follows. 

<img src="https://drive.google.com/uc?id=1ZL-RXqFmERa1gxxpbid7yz00azMNXRoY"
     alt="sample image"
     style="display: block; margin-right: auto; margin-left: auto; width: 90%;
     box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19)" />

By doing the above, we are only loosing less than 4% of the variation in the data.

## Discussion on no. of variables.

Usually we have a plethora of variables and applying this method reduces the no. of variables by a lot. The least variation covering PCs could be considered noise or not affecting the data. If there are 2 variables that are depended on each other (or correlated), PCA approach collapses that to 1, and make the other obsolelete, like the example above. 

One of the most important discussion should be about how many variables or PC should be used. This is the discussion that is rarely seen in the tutorials and projects. The cumulative variation covered by the no. of PCs should be the motivating factor to determine the number of PCs. And the no. of variation covered should be determined by the project. It should not be directly assuming we want certain no. of variables.

For example if there were 15 variables and 15 PCs. If 6 of them covered 90% and 8 of them covered 95% and 10 of them covered 98%, so if the project requires or defines 95%, then 8 of them should be used. 

## The aftermath of PC

After getting the PCs, we have reduced the number of variables but the data has to be fitted (that is fit the z values with this eigenvalue). In our original problem, we had z as a function of x and y. Now we are just fitting z as a function of PC1. This way the problem is reduced from 2 variables to 1. For a large datasets, the PC approach reduces the no. of variables by half or more. Therefore it is an important tool.

* For a new set of data ie, new x and new y, we map the new variables to the pca dimension and select that now value and use the fit parameter to get the new z value.

* The error for these fits could be intrapolation if the new variables (x,y) are within the previously mapped (x and y). if so, the translated error of the fit is generally lower.

* If the parameters are outside the perviously determined range, the errors are generally bigger than the previously fitted variables. 