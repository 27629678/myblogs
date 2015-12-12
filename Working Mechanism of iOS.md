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

####The Solution

Fast Image Cache minimizes (or avoids entirely) much of the work described above using a variety of techniques:

#####Mapped Memory

At the heart of how Fast Image Cache works are image tables. Image tables are similar to sprite sheets, often used in 2D gaming. An image table packs together images of the same dimensions into a single file. This file is opened once and is left open for reading and writing for as long as an application remains in memory.

Image tables use the mmap system call to directly map file data into memory. No memcpy occurs. This system call merely creates a mapping between data on disk and a region of memory.

When a request is made to the image cache to return a specific image, the image table finds (in constant time) the location of the desired image data in the file it maintains. That region of file data is mapped into memory, and a new CGImageRef whose backing store is the mapped file data is created.

When the returned CGImageRef (wrapped into a UIImage) is ready to be drawn to the screen, iOS's virtual memory system pages in the actual file data. This is another benefit of using mapped memory; the VM system will automatically handle the memory management for us. In addition, mapped memory "doesn't count" toward an application's real memory usage.

In like manner, when image data is being stored in an image table, a memory-mapped bitmap context is created. Along with the original image, this context is passed to an image table's corresponding entity object. This object is responsible for drawing the image into the current context, optionally further configuring the context (e.g., clipping the context to a rounded rect) or doing any additional drawing (e.g., drawing an overlay image atop the original image). mmap marshals the drawn image data to disk, so no image buffer is allocated in memory.

#####Uncompressed Image Data

In order to avoid expensive image decompression operations, image tables store uncompressed image data in their files. If a source image is compressed, it must first be decompressed for the image table to work with it. This is a one-time cost. Furthermore, it is possible to utilize image format families to perform this decompression exactly once for a collection of similar image formats.

There are obvious consequences to this approach, however. Uncompressed image data requires more disk space, and the difference between compressed and uncompressed file sizes can be significant, especially for image formats like JPEG. For this reason, Fast Image Cache works best with smaller images, although there is no API restriction that enforces this.

#####Byte Alignment

For high-performance scrolling, it is critical that Core Animation is able to use an image without first having to create a copy. One of the reasons Core Animation would create a copy of an image is improper byte-alignment of the image's underlying CGImageRef. A properly aligned bytes-per-row value must be a multiple of 8 pixels × bytes per pixel. For a typical ARGB image, the aligned bytes-per-row value is a multiple of 64. Every image table is configured such that each image is always properly byte-aligned for Core Animation from the start. As a result, when images are retrieved from an image table, they are already in a form that Core Animation can work with directly without having to create a copy.