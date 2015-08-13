## - 介绍相关的API

iOS 7 自定义视图控制器转场的 API 基本上都是以协议的方式提供的，这也使其可以非常灵活的使用，因为你可以很简单地将它们插入到你的类中。最主要的五个组件如下:

- 动画控制器 (Animation Controllers) 遵从 UIViewControllerAnimatedTransitioning 协议，并且负责实际执行动画
- 交互控制器 (Interaction Controllers) 通过遵从 UIViewControllerInteractiveTransitioning 协议来控制可交互式的转场
- 转场代理 (Transitioning Delegates) 根据不同的转场类型方便的提供需要的动画控制器和交互控制器
- 转场上下文 (Transitioning Contexts) 定义了转场时需要的元数据，比如在转场过程中所参与的视图控制器和视图的相关属性。 *转场上下文对象遵从 UIViewControllerContextTransitioning 协议，并且这是由系统负责生成和提供的*
- 转场协调器(Transition Coordinators) 可以在运行转场动画时，并行的运行其他动画。 转场协调器遵从 UIViewControllerTransitionCoordinator 协议

> **NOTE:**正如你从其他的阅读材料中得知的那样，转场有不可交互式和可交互式两种方式。在本文中，我们将集中精力于不可交互的转场。这种转场是最简单的转场，也是我们学习的一个好的开始。这意味着我们需要处理上面提到的动画控制器 (animation controllers)，转场代理 (transitioning delegates) 和转场上下文 (transitioning contexts)。

-------------

# · 接下来我们分头讲以上5个部分：

----------

## - UIViewControllerAnimatedTransitioning

在实现视图控制器转场动画的对象上遵从`<UIViewControllerAnimatedTransitioning>`协议，协议提供了方法让用户定义一个动画对象**(Animator object)**。该对象可以创建一个VC在固定的时间内进入或者离开屏幕的动画。被创建的动画对象为不可以交互式的动画，若要做交互式的动画需要绑定一个控制动画时间的对象，后面再分别介绍。

在动画对象中实现`transitionDuration: `方法用于指定动画执行的时间，实现`animateTransition: `方法用于执行具体的动画，即将目标View移入屏幕并将指定的View移出屏幕，该方法有个上下文对象(context object)提供了动画执行所需要的全部信息。

从**transitioning delegate**(一个实现了`UIViewControllerTransitioningDelegate`协议的对象)中创建动画对象（Animator object)。当Presenting一个VC时，设置其PresentStyle为**UIModalPresentationCustom**并设置VC的`transitioningDelegate`属性为**transitioning delegate**，VC从**transitioning delegate**中找动画对象（Animator object)去执行动画。用户可以为VC的**Presenting**和**Dismissing**指定不同的动画对象。

若要使用交互式的转场动画，动画对象(Animator object)必须与实现了`UIViewControllerInteractiveTransitioning`协议的**interactive animator object**对象一起使用。更多关于[UIViewControllerInteractiveTransitioning Protocol Reference][1]

#### Performing a Transition

- \- animateTransition: *Required*
- \- animationEnded:

#### Reporting Transition Duration

- \- transitionDuration:*Required*

<!-- 网址链接 -->
[1]:https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewControllerInteractiveTransitioning_protocol/index.html#//apple_ref/doc/uid/TP40013059

-------------

## - UIViewControllerTransitioningDelegate

一个实现`UIViewControllerTransitioningDelegate`协议的对象维护着一些用于管理定长或者交互式转场动画所需要的所有对象。当用户Present一个VC时，设置`modalTransitionStyle`属性为**UIModalPresentationCustom**并且设置VC的`transitioningDelegate`的属性为一个实现了**UIViewControllerTransitioningDelegate**协议的对象。当要Present时，UIKit通过**transitioning delegate**查找执行动画所需要的所有对象。

在实现`transitioning delegate`代码功能时，用户可以根据不同的情况（主要基于VC是被Presented还是被Dismissed）提供不同的Animator object。所有的转场都使用一个animator object完成基本的动画。

对于自定义的模态转场动画，用户可以为animator object提供一个`UIPresentationController`对象。我们可以使用PresentationController来协调presentation和dismissal过程中比较难以实现的步骤。例如，要创建一个阴影View放于内容View的下面，此时我可以通过PresentationContrller来维护何时添加和移除该阴影View。

#### Getting the Transition Animator Objects

- \- animationControllerForPresentedController:presentingController:sourceController:
- \- animationControllerForDismissedController:

#### Getting the Interactive Animator Objects

- \- interactionControllerForPresentation:
- \- interactionControllerForDismissal:

#### Getting the Custom Presentation Controller

- \- presentationControllerForPresentedViewController:presentingViewController:sourceViewController:

--------------

## - UIViewControllerTransitionCoordinator

一个实现了``协议的对象为VC的转场动画提供了强力的支持。若用户没有自定义实现该协议，在Present或者Dismiss的时候，UIKit自动创建一个Cordinator对象并将该对象赋值给VC的`transitionCoordinator`属性。该Cordinator对象生命周期非常短暂（仅存活于整个转场动画执行期内）。

Transation Cordinator可以执行一些与转场动画相关的任务，一些与Animator对象无法完成的隔离开的任务。转场动画执行过程中，Animator对象用于将一个VC的View呈现出来，但有可能其它一些可见的元素也需要做动画，这时就若要Cordinator对象来完成了。

Transation Cordinator与Animator object一起确保任何额外的动画与转场动画一起执行。这意味着所有与转场动画相关的动画同时执行且根据Interactive Animator对象提供的时间做出响应。它所做一切无需添加额外的代码。

除此之外，可以调用``方法注册一个回调Block用于执行清理工作（复位动画执行前的操作）。在交互式转场动画中，清理工作是非常重要的。

因为Transation Cordinator仅在转场动画期间有效，所以UIKit会在转场动画执行完成后释放该对象。

#### Responding to View Controller Transition Progress

- \- animateAlongsideTransition:completion:*Required*
- \- animateAlongsideTransitionInView:animation:completion:*Required*
- \- notifyWhenInteractionEndsUsingBlock:*Required*

----------------

## - UIViewControllerContextTransitioning

`UIViewControllerContextTransitioning`协议中的方法为VC之间的转场动画提供了上下文信息。转场动画执行期间，Animator Object从UIKit接收一个Fully Configured Context对象。

一个上下文对象封装了所有关于View和VC转场动画执行所有需要的信息，同时也包含了转场动画如何执行的详情。以交互式转场动画为例，Interactive Animator对象依靠该协议提供的方法抛出动画执行的进度。

> **NOTE:**Do not adopt this protocol in your own classes, nor should you directly create objects that adopt this protocol.

#### Accessing the Transition Objects

- \- containerView*Required*
- \- viewControllerForKey:*Required*
- \- viewForKey:*Required*

#### Getting the Transition Frame Rectangles

- \- initialFrameForViewController:*Required*
- \- finalFrameForViewController:*Required*

#### Getting the Transition Behaviors

- \- isAnimated*Required*
- \- isInteractive*Required*
- \- presentationStyle*Required*

#### Reporting the Transition Progress

- \- completeTransition:*Required*
- \- updateInteractiveTransition:*Required*
- \- finishInteractiveTransition*Required*
- \- cancelInteractiveTransition*Required*
- \- transitionWasCancelled*Required*

#### Getting the Rotation Factor
- \- targetTransform*Required*

#### Constants

- View Controller Transition Keys
- View Transition Keys
