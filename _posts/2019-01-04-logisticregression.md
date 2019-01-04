---
layout: post
title: "logistic regression"
date: 2019-01-04
tags: 机器学习
---

参数更新  
$$
    \theta_{j} := \theta_{j} - \alpha \frac{\partial}{\partial \theta_j} J(\theta)
$$  
其中:  
$$
    \frac{\partial}{\partial \theta_j} J(\theta) = \frac{1}{m} \sum_{i=1}^{m}(h_{\theta}(x^{i}) - y^{i}) * x^{i}_{j}
$$


