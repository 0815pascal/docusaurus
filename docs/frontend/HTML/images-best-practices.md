---
sidebar_position: 6
---
# Web Images: Best Practices

![Web Images: Best Practices](img/FSkQSw8XsAIkUNv.jpg)

## When to use background-image or `<img>`

- Use the `<img>` tag when the image is related to the content and helps people understand it better.
- Use CSS `background-image` when the image is a part of the page presentation or visual design

## Choose the right image format

- Vector formats (e.g. svg) are ideally suited for images that consist of simple geometric shapes such as logos, text, or icons. 
- When the scene is complicated (for example, a photo) use PNG, JPEG, or WebP

|Format|Transparency   |Animation   |Browser            |
|------|---------------|------------|-------------------|
|PGN   |Yes            |No          |All                |
|JPEG  |No             |No          |All                |
|WebP  |Yes            |Yes         |All modern browsers|

The WebP format will generally provide better compression than older formats, and should be used where possible.

You can use WebP along with another image format as a fallback:

```html
<picture>
    <source type="image/webp" srcset="flower.webp">
    <source type="image/jpeg" srcset="flower.jpg">
    <img src="flower.jpg" alt="">
</picture>
```

## Serve images with correct dimensions
Use **srcset** and **sizes** attributes to serve different images to different display densities. 

- If the device has a low resolution display (1x), the photo-320w.jpg image will be loaded.
- If the device has a higher resolution (2x) the photo-640w.jpg image will be loaded.
- **src** attribute acts as a fallback

```html
<img srcset="photo-320w.jpg 1x, 
             photo-480w.jpg 1.5x, 
             photo-640w.jpg 2x"
    src="photo-640w.jpg"
    alt="some awesome photo" />
```

**"Display density** refers to the fact that different displays have different densities of pixels, a high pixel density display will look sharper than a low pixel density display. 

## Consider using image CDNs to optimize images
Image **CDNs** specialize in the transformation, optimization, and delivery of images. 

For images loaded from an image **CDN**, an image URL indicates not only which image to load, but also parameters like size, format, and quality. 

```html
<img src="dog.jpg?size=small" />
<img src="dog.jpg?crop=circle" />
<img src="dog.jpg?format=webp" />
```

## Use lazy-loading to improve loading speed
You can add the **loading** attribute and set its value to **lazy** to defer the loading of offscreen images until they become visible in the browser.

```html
<img loading="lazy" src="awesome-image.webp" alt="awesome image" />
``` 
This tells the browser "only download this image when it's visible to the user"

**Source**: [@_georgemoller](https://mobile.twitter.com/_georgemoller/status/1524767909129732097)
