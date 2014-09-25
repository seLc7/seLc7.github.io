---
title: '又是Matlab'
layout: post
tags:
    - tech
---
今天的任务是生成一个对数坐标拟合图。采用的对比曲线是Zeta distribution，长尾分布的一种。

    y= hist(outRate,50);

    y1=y/sum(y); % 调整纵坐标比利

    pingjun=mean(y1)  % 均值
    %s=std2(y1); %均方差
    fangcha = var(y1)  % 方差
    vr=sum((y1(1,:)-mean(y1)).^2)/(length(y1)-1) %也是方差

    zeta_y = zeta_distribution(50,3.95);

    figure(1);
    loglog(1:50,y1,'.r');
    hold on;  %在一个图里生成图像
    loglog(1:50,zeta_y);

    set(gca,'xgrid','on');

上面的程序段即是Matlab中生成对比图像的代码段。`loglog(1:50,y1,'.r');`中的`.r`表示红色的点状散点，也可以声明为`+r`等。最后一行的`set(gca,'xgrid','on');`表示只显示x轴的grid网格线。`zeta_distribution`即是生成zeta曲线的函数，其代码如下：

    % zeta distribution is one of typical discrete heavy-tailed distributions.
    % 输入: x = [1,2,...,data_length], parameter_tao为函数可调参数.
    function [ data_result_y ] = zeta_distribution( data_length, parameter_tao )

    clear data_result_x;
    data_result_x = 1:data_length;

    clear data_result_y;
    for i = 1:data_length
        data_result_y(i) = 1 / (data_result_x(i)^parameter_tao * zeta(parameter_tao));
    end
    end

其他查找到的**tips**：

*   `subplot()`可以同时生成两个图在一个对话框中。
*   上面的两个文件保存为.m格式，需在统一目录下才能查找到zeta_distribution函数。
