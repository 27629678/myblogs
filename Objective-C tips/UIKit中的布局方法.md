## UIKit中的布局方法

### 1、UIView

```
@interface UIView(UIViewHierarchy)

// 以下两个方法容易混淆，主要区别是刷新时机
// 以做约束动画为例就明白两者的区别了
- (void)setNeedsLayout;
- (void)layoutIfNeeded;

- (void)layoutSubviews;

@end

// 该分类内的方法不常用，但是非常有用
@interface UIView (UIConstraintBasedLayoutCoreMethods)

- (void)updateConstraintsIfNeeded;
- (void)updateConstraints;
- (BOOL)needsUpdateConstraints;
- (void)setNeedsUpdateConstraints;

@end

@interface UIView(UIViewRendering)

- (void)drawRect:(CGRect)rect;

// 以下方法配合drawRect:方法使用
- (void)setNeedsDisplay;
- (void)setNeedsDisplayInRect:(CGRect)rect;

@end
```

### CALayer

```
@interface CALayer : NSObject <NSSecureCoding, CAMediaTiming>

/* Marks that -layoutSublayers needs to be invoked on the receiver
 * before the next update. If the receiver's layout manager implements
 * the -invalidateLayoutOfLayer: method it will be called.
 *
 * This method is automatically invoked on a layer whenever its
 * `sublayers' or `layoutManager' property is modified, and is invoked
 * on the layer and its superlayer whenever its `bounds' or `transform'
 * properties are modified. Implicit calls to -setNeedsLayout are
 * skipped if the layer is currently executing its -layoutSublayers
 * method. */

- (void)setNeedsLayout;

/* Returns true when the receiver is marked as needing layout. */

- (BOOL)needsLayout;

/* Traverse upwards from the layer while the superlayer requires layout.
 * Then layout the entire tree beneath that ancestor. */

- (void)layoutIfNeeded;

/* Called when the layer requires layout. The default implementation
 * calls the layout manager if one exists and it implements the
 * -layoutSublayersOfLayer: method. Subclasses can override this to
 * provide their own layout algorithm, which should set the frame of
 * each sublayer. */

- (void)layoutSublayers;


/* Reload the content of this layer. Calls the -drawInContext: method
 * then updates the `contents' property of the layer. Typically this is
 * not called directly. */

- (void)display;

/* Marks that -display needs to be called before the layer is next
 * committed. If a region is specified, only that region of the layer
 * is invalidated. */

- (void)setNeedsDisplay;
- (void)setNeedsDisplayInRect:(CGRect)r;

/* Returns true when the layer is marked as needing redrawing. */

- (BOOL)needsDisplay;

/* Call -display if receiver is marked as needing redrawing. */

- (void)displayIfNeeded;

@end
```