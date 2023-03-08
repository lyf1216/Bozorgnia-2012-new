# Bozorgnia-2012-new

case 1（层流模型）:
    初始和边界条件:
    
    1）U——
	①初始的内部场为(0,0,0)（1、初始的内部场可随意设置，setWaveField命名会根据waveProperties中的设置对整个网格的U内部场进行更新，其中自由面以上为风速，自由面以下则根据waveProperties字典中initializationName的方法进行更新）
	
	②inlet为zeroGradient（1、边界值由松弛区的值决定，因此应将松弛区的高度设置为整个inlet范围；2、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建U时会令相应边界值等于邻近内部单元值）；

	③壁面为fixed value，值为(0,0,0)（1、壁面采用无滑移边界条件，在整个求解过程中不会发生变化；2、在createFields.H中创建U时会令相应边界值等于0/U字典中value对应的值）；
	
	④outlet为fixed value，值为(0,0,0)（1、出口处水体速度为0，与出口的松弛区速度设置一致，因此也可采用zeroGradient；2、由于边界值应与邻近单元的值协调，即均为0，因此应将松弛区的高度设置为整个outlet范围）；
	
	⑤atmosphere为pressureInletOutletVelocity（1、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建U时会令相应边界值等于0/U字典中value对应的值；2、该边界在更新时根据通量的方向确定U边界值，当通量为正，即outflow，则为0梯度边界条件；当通量为负，即inflow，则边界的U为内部单元值与边界面垂直的分量）
    
    2）p_rgh(动压，减去了静压值，且湍流的影响已考虑)——
	①初始的内部场为0（1、初始的内部场可随意设置，setWaveField命名会根据waveProperties中的设置对整个网格的p内部场进行更新，其中自由面以上为0，自由面以下则根据waveProperties字典中initializationName的方法进行更新）

	②inlet为zeroGradient（1、边界值由松弛区的值或计算值决定，因此应将松弛区的高度设置为整个inlet范围；2、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建U时会令相应边界值等于邻近内部单元值）；

	③壁面为zeroGradient（1、因此近壁面单元的压力的准确性对于作用在结构上的力至关重要）

	④outlet均为zeroGradien（1、当参考水平面与海平面一致时，出口处水体的动压应为0，因为该处流场速度为0；2、应将松弛区的高度设置为整个outlet范围）

	⑤atmosphere为totalPressure（1、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建p_rgh时会令相应边界值等于0/p_rgh字典中value对应的值；2、该边界条件提供了一个总压条件，存在4种变体，具体采用哪种取决于压力场的量纲，psi entry和gamma的值）
    
    3）alpha.water(也为alpha1，即水的相分数)——
    
    ①初始的内部场为0（1、初始的内部场可随意设置，setWaveField命名会根据waveProperties中的设置对整个网格的alpha1内部场进行更新，其中自由面以上为0，自由面以下则为单元的自由面以下体积/单元体积）

    ②inlet为zeroGradient（1、边界值由松弛区的值决定，因此应将松弛区的高度设置为整个inlet范围；2、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建U时会令相应边界值等于邻近内部单元值）；

	③壁面为zeroGradient（）

	④outlet均为zeroGradient（1、outlet的相分数分布应与初始时刻相同，即自由面以上为空气，自由面以下为水；2、应将松弛区的高度设置为整个outlet范围）

	⑤atmosphere为inletOutlet（1、执行setWaveField命名时不会对边界值进行修改，但在createFields.H中创建alpha1时会令相应边界值等于0/alpha.water字典中value对应的值；2、inletValue的值为refValue_；3、该边界在更新时根据表面通量的方向确定alpha1取值，当通量为正（流出计算域）时，采用0梯度条件；当通量为负（流入计算域）时，采用fixed value条件，fixed valued的值即为refValue_）

    4）k（湍流动能）——

    ①初始的内部场为0（1、初始时刻整个计算域的速度为0，因此湍流动能为0）

    ②inlet为turbulentIntensityKineticEnergyInlet（1、在createFields.H中创建k时会令相应边界值等于0/k字典中value对应的值；2、intensity对应相应的湍流强度，5%对应中等及高湍流强度的临界值；3、更新时按照mixed边界条件的公式计算k）

    ③壁面为kqRWallFunction（1、该边界实际上等效于zeroGradient边界；2、边界值取为内部单元值）

    ④outlet为zeroGradient

    ⑤atmosphere为inletOutlet（1、在createFields.H中创建k时会令相应边界值等于0/k字典中value对应的值；2、inletValue的值为refValue_；3、该边界在更新时根据表面通量的方向确定k取值，当通量为正（流出计算域）时，采用0梯度条件；当通量为负（流入计算域）时，采用fixed value条件，fixed valued的值即为refValue_）

    5）epsilon（湍流动能耗散率）——

    ①初始的内部场为0（1、初始时刻整个计算域的速度为0，湍流动能为0，因此耗散也应为0）

    ②inlet为turbulentMixingLengthDissipationRateInlet（1、在createFields.H中创建epsilon时会令相应边界值等于0/epsilon字典中value对应的值；2、mixingLength对应相应的混合长度，用于计算epsilon的fixed value部分；3、更新时按照mixed边界条件的公式计算epsilon）

    ③壁面为epsilonWallFunction（1、初始时边界值取为内部单元值）

    ④outlet为zeroGradient

    ⑤atmosphere为inletOutlet（1、在createFields.H中创建epsilon时会令相应边界值等于0/epsilon字典中value对应的值；2、inletValue的值为refValue_；3、该边界在更新时根据表面通量的方向确定epsilon取值，当通量为正（流出计算域）时，采用0梯度条件；当通量为负（流入计算域）时，采用fixed value条件，fixed valued的值即为refValue_）

    5）nut（湍流粘度）——

    ①初始的内部场为0（1、初始的内部场可随意设置，创建湍流模型时会对nut进行更新，根据k和epsilon计算）

    ②inlet为zeroGradient

    ③壁面为nutkWallFunction（1、该壁面函数根据k和y+的值确定壁面的nut取值，为层流区域和湍流区域nut值的blending值；2、初始时边界值取为value的值；3、创建湍流模型时会对该边界值进行更新，根据k和y+计算）

    ④outlet为zeroGradient

    ⑤atmosphere为calculated（1、初始时边界值取为value的值；2、创建湍流模型时会对该边界值进行更新，与内部场一起更新，根据k和epsilon计算）



    求解:
    1）第一次设置的求解在0.6s后即无法收敛
    2）第二次设置将PIMPLE的外部循环最大调至100，残差控制均设置为1e-3，但每个时间步均需迭代满100次，且前几次迭代后解的残差变化明显减小
    3）第三次设置将PIMPLE的外部循环最大调至20，且最大Courant number设置为0.5，因为前一次分析结果表明5次迭代后结果变化不大	
    4）第四次设置将PIMPLE的外部循环最大调至5，但设置Courant number使得2s后的时间步长变为1e-7，过小，因此应进一步分析y+和原因。CoNum的计算公式为0.5*gMax(sumPhi/mesh.V().field())*runTime.deltaTValue()，因此phi的计算结果的正确性影响较大。查看log.waveFoam，发现2.07557s的Ux方程的Final residual为120.074，因此判断x方向动量方程的预测步求解过程中的误差导致
    
case 2（k-epsilon模型）:
    1）设置k-epsilon湍流模型，以探究其对x方向动量方程求解误差的影响






