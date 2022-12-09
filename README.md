# Bozorgnia-2012-new

case 1:
    1）第一次设置的求解在0.6s后即无法收敛
    2）第二次设置将PIMPLE的外部循环最大调至100，残差控制均设置为1e-3，但每个时间步均需迭代满100次，且前几次迭代后解的残差变化明显减小
    3）第三次设置将PIMPLE的外部循环最大调至20，且最大Courant number设置为0.5，因为前一次分析结果表明5次迭代后结果变化不大	
    4）第四次设置将PIMPLE的外部循环最大调至5，但设置Courant number使得2s后的时间步长变为1e-7，过小，因此应进一步分析y+和原因。CoNum的计算公式为0.5*gMax(sumPhi/mesh.V().field())*runTime.deltaTValue()，因此phi的计算结果的正确性影响较大。查看log.waveFoam，发现2.07557s的Ux方程的Final residual为120.074，因此判断x方向动量方程的预测步求解过程中的误差导致
    5）第五次设置考虑湍流模型，以探究其对x方向动量方程求解误差的影响

