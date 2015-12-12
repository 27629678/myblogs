###Working Mechanism of iOS

####1.[UIImage imageWithContentOfFile:]

Consider the workflow that occurs when loading an image from disk and displaying it on screen:

1. +[UIImage imageWithContentsOfFile:] uses Image I/O to create a CGImageRef from memory-mapped data. At this point, the image has not yet been decoded.
2. The returned image is assigned to a UIImageView.
3. An implicit CATransaction captures these layer tree modifications.
4. On the next iteration of the main run loop, Core Animation commits the implicit transaction, which may involve creating a copy of any images which have been set as layer contents. Depending on the image, copying it involves some or all of these steps:
	- Buffers are allocated to manage file IO and decompression operations.
	- The file data is read from disk into memory.
	- The compressed image data is decoded into its uncompressed bitmap form, which is typically a very CPU-intensive operation.
	- The uncompressed bitmap data is then used by Core Animation to render the layer.

**These costs can easily accumulate and kill perceived application performance.** Especially while scrolling, users are presented with an unsatisfying user experience that is not in line with the the overall iOS experience.

------

1 60FPS ≈ 0.01666s per frame = 16.7ms per frame. This means that any main-thread work that takes longer than 16ms will cause your application to drop animation frames.

2 The documentation for CALayer's contents property states that "assigning a value to this property causes the layer to use your image rather than [creating] a separate backing store." However, the meaning of "use your image" is still vague. Profiling an application using Instruments often reveals calls to CA::Render::copy_image, even when the Core Animation Instrument has indicated that none of the images have been copied. One reason that Core Animation will require a copy of an image is improper byte alignment.

3 As of iOS 7, Apple does not make their hardware JPEG decoder available for third-party applications to use. As a result, only a slower, software decoder is used for this step.