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