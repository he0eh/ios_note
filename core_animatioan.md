#Core Animatioan
======
## 1、View自身修改属性
	frame	大小以及位置
	transform	缩放和旋转
	alpha	透明度
	duration 出现在屏幕上的时间 立刻生效
	delay	延迟执行时间
	options	
	animations	修改frame，transform等，主要的修改代码
	completion	动画执行完成或中断（被打断，通过finished区分）之后调用
	
	animations中的修改立即执行，但效果需要时间显示
	此方法属于类方法，具体实现未知
	
	+ (void)animateWithDuration:(NSTimeInterval)duration 
						   delay:(NSTimeInterval)delay
						 options:(UIViewAnimationOptions)options 
					   animations:(void (^)(void))animations 
					   completion:(void (^)(BOOL finished))completion;
					   
	Example:
	三秒钟myView消失
    [UIView animateWithDuration:3.0
                          delay:0
                        options:UIViewAnimationOptionBeginFromCurrentState
                     animations:^{
                         myView.alpha = 0.0;
                     }
                     completion:^(BOOL fin) {
                         if (fin) {
                             [myView removeFromSuperview];
                         }
                     }];
    

###UIViewAnimationOptions
	UIViewAnimationOptionLayoutSubviews            // 跟父类作为一个整体一起动画，此方式为默认方式
    UIViewAnimationOptionAllowUserInteraction      // turn on user interaction while animating // 在动画运行过程中，允许用户与之交互操作
    UIViewAnimationOptionBeginFromCurrentState     // start all views from current value, not initial value // 从当前状态开始动画
    UIViewAnimationOptionRepeat                    // repeat animation indefinitely // 重复执行一个动画，从初始状态到结束状态，然后瞬间跳到初始状态继续无限次的执行同一动作  
    UIViewAnimationOptionAutoreverse               // if repeat, run animation back and forth // 反向执行一个动画。当动画执行完一遍后，沿原路径反向执行一遍。这个属性必须跟UIViewAnimationOptionRepeat一起使用
    UIViewAnimationOptionOverrideInheritedDuration // ignore nested duration // 忽略子层嵌套的动画的时间间隔。例如如下代码
    UIViewAnimationOptionOverrideInheritedCurve    // ignore nested curve // 忽略子层嵌套的动画属性的时间
    UIViewAnimationOptionAllowAnimatedContent      // animate contents (applies to transitions only) // 允许同一个view的多个动画同时进行
    UIViewAnimationOptionShowHideTransitionViews   // flip to/from hidden state instead of adding/removing // 控制两个subview的显示和隐藏
    UIViewAnimationOptionOverrideInheritedOptions  // do not inherit any options or animation type 
    
    // ========= 动画过渡动作的速度
    UIViewAnimationOptionCurveEaseInOut            // default // 先慢后快再慢
    UIViewAnimationOptionCurveEaseIn               // 先慢后快
    UIViewAnimationOptionCurveEaseOut              // 先快后慢
    UIViewAnimationOptionCurveLinear               // 匀速
    
    // ========= 动画过渡过程的方式：
    UIViewAnimationOptionTransitionNone            // default // 不指定方式
    UIViewAnimationOptionTransitionFlipFromLeft    // 翻转 
    UIViewAnimationOptionTransitionFlipFromRight   // 翻转
    UIViewAnimationOptionTransitionCurlUp          // 翻转
    UIViewAnimationOptionTransitionCurlDown        // 翻转
    UIViewAnimationOptionTransitionCrossDissolve   // 重叠，当一个view从一个位置到另一个位置时，当前的view会由透明状态逐渐显示到目的位置，原来的位置将会保留一个影子，并逐渐消失
    UIViewAnimationOptionTransitionFlipFromTop     // 翻页 
    UIViewAnimationOptionTransitionFlipFromBottom  // 翻页 
    
    以上option解释copy自http://blog.csdn.net/namehzf/article/details/7650416
##2、View状态切换／View之间切换的动画

	View状态之间切换
	实现原理也是两个View之间的切换，系统自动生成了一个状态修改过的View，和原来的View之间切换
	属性同1
	+ (void)transitionWithView:(UIView *)view 
					  duration:(NSTimeInterval)duration 
					   options:(UIViewAnimationOptions)options 
					animations:(void (^)(void))animations 
					completion:(void (^)(BOOL finished))completion;		
	Example:
	卡牌游戏中，卡牌对象通过up属性来表示正反面，并通过此属性来控制绘制
	一秒钟把牌从左边用带有翻页动画的样式反过来
    [UIView transitionWithView:cardView
                      duration:1.0
                       options:UIViewAnimationOptionTransitionFlipFromLeft
                    animations:^{
                        cardView.up = NO;
                    }
                    completion:^(BOOL fin) {
                         if (fin) {
                             [myView removeFromSuperview];
                         }
                     }];

	View之间切换
	View状态切换的真实实现
	+ (void)transitionFromView:(UIView *)fromView 
						 toView:(UIView *)toView 
					   duration:(NSTimeInterval)duration 
						options:(UIViewAnimationOptions)options 
					 completion:(void (^)(BOOL finished))completion; // toView added to fromView.superview, fromView removed from its superview

##3、Dynamic Animation （IOS 7）
	Behavior //行为
		UIGravityBehavior //重力 
			angle //重力方向，默认向下（可任意设置方向）
			magnitude //重力大小，默认为1（1000 points/s/s）
		UICollisionBehavior	//碰撞 
			collosionMode // Items, Boundaries, Everything(default) 弹开类型：相互弹开，边界弹开，全部
			translatesReferenceBoundsIntoBoundary //是否弹性边界
		UIAttachmentBehavior //吸附行为   将动力项（View）吸附到锚点上，可吸附多个
			比较复杂，拖动view可以用次实现，效果甚好
		UISnapBehavior //不知道怎么翻译，就是那种晃动了一下动画，抖了一下，告诉用户有反馈
		UIPushBehavior //推动力，可指定角度和推力大小，和重力有点类似
		Behavior中拥有action属性为block，在Behavior发生作用时会重复调用，类似拖动方法，勿在此block中使用耗费资源的操作，否则会造成动画卡顿
	
	定义动画使之作用于view，作用会一直持续，添加行为后立即执行
	使用
		//定于UIDynamicAnimator
		[[UIDynamicAnimator alloc] initWithReferenceView:aView];
		//aView是指顶层View，动画可移动的范围
		//添加行为（重力，碰撞等等）
		UIGravityBehavior *gravity = [[UIGravityBehavior alloc]init];
		gravity.magnitude = 0.9;//重力设置为0.9
    	[self.animator addBehavior:gravity];
    	UICollisionBehavior *collision = [[UICollisionBehavior alloc]init];
    	collosion.translatesReferenceBoundsIntoBoundary= YES;
    	[self.animator addBehavior:collision];
		//添加项目（执行者，大多数为view，也可以是实现了UIDynamicItem协议的非视觉项目），添加到行为中
		[self.gravity addItem:dropView];
    	[self.collosion addItem:dropView];
    	
    	在不同类型的动画出现叠加时应调用updateItemUsingCurrentState，否则只执行Dynamic Animation(有待测试)
    	多个行为叠加习惯做法是封装自定义Behavior使用addChildBehavitor进行组合

		