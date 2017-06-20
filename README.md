# Fluent Conference Notes

Notes from Fluent Conf

- TL;DR Web Performance
- Serverless Framework + AWS

---

## TL;DR Web Performance

> Gareth Hughes (Akamai), Michael Gooding (Akamai)

### Outline

1. Compression
2. Images
3. Page Structure
4. HTTPS & HTTP/2
5. Resource Hints


Time is money. Performance encompasses time. The faster the website, the more money it will make. Case studies for various performance related improvements: http://wpostats.com

#### Webperf 101

> https://www.webpagetest.org/

> https://whatdoesmysitecost.com/

- Measurement
  * You can't optimize what you can't measure; you can't move faster than the speed of light
- Distance
  - How far is the content being sent?
- Size
  - Bundles, optimization
- Optimize
  - Devices
    * Responsive Design
    * Image sizes
  - Browsers
    * Image formats
    * Progressive enhancements
  - Networks
    * IPV6
    * HTTp/2
    * Round trip times (RTT)
    * 3G -> 4G -> patchy network

#### What you need to know

* Cache is king
* https://www.youtube.com/watch?v=Yg1kYGUrSok


#### Compression

* Shrinking text objects - html, css, js
* Compression is taking patterns in a block of text replacing with references (sourcemaps)
* gzthermal - heatmaps
* Why is this important?

```
HTML -> DOM
         ^
         JS -> Render -> Paint
         ^
CSS -> CSSDOC
```

* Gzip, Brotli, Zopfli for compression - the latter are newer algorithms
* Zopfli has been around for 3 years and is backwards compat with Gzip
  * 16% faster
* Brotli is newer and requires a middleman to work with Gzip and only served over https
  * 23% Faster
  * nginx handles it out of the box
  * mod_brotli for apache
  * iis Brotli (paid)
- Who is using this compression? Everyone! Hopefully.
- 98% of pages have at least 1 compressed object
- Brotli adoption is 54%
* Gzip is adopted widely amonst the industry (2.3% isn't using it)
* Brotli is better at compression but a bit slower
* Investigate using Brotli for static content!

#### Images

* Two different optimization of Images
  - Create Images
    - Size & shape for device
    - Formats (SVG, jpggxp, webp, png, gif, jpeg)
    - Quality of images (compression & size)
  - Deliver images
    - smaller images download faster
    - Consider network performance
    - smaller images also load on device faster
    - Using Responsive images
    - Adaptive images
    - Javascript based solutions... aim for native, though
    - Lazy loading images
      1. Delay non-critical images until after onload
      2. As you scroll down the page (mobile/responsive websites)
      3. Load on a user event (such as a click or hover)
* Majority of content on the web is an image (http archive, May 2017)
* Why serve the right size image?
  - Cellular & WiFi
  - Mobile Data plans can be expensive ($$$$)
  - Mobile Device constraints
    - Mobile devices are not PCs
    - Not as much memory / processor power
* What to focus on when optimizing..
  - Size, Format, quality

##### **Image Strategy 101**

1. On demand or create up front?
2. DIY or commercial offering
3. Choose a delivery method
  * On demand images
    - only create images when needed
    - No wasted storage
    - Potentially slower first time images
    - lack of control
  * Create Upfront
    - ALways fast response
    - Slow time to market
    - Changes to images can take days
  * DIY solutions
    - mozjepg
    - Image Magick & imagetragick.com
    - imageoptim
    - kraken.io
    - jpegmini
    - tinypng
  * Commercial solutions
    - Akamai
    - Fastly
    - imgIX
    - adobe scene 7
    - amplience
    - cloudinary

##### Responsive Images

* srcset & sizes
```HTML
<img src='lol.png' srcset="small.jpg 400w, large.jpg 800w"
  sizes="(max-wiidth:400px) 400px, 800px"
  alt='lolcats' />
```
- picture tag
```HTML
<picture>
  <source type='image/webp' media='(max-width:400px)' srcset='small.webp' />
  <source type='image/png' media='(max-width:800px)'
  srcset='large.png' />
</picture>
```
- Client Hints - `<meta htttp-equiv="Accept-CH" content="DPR" />`
- Use SVG for icons!
- Jpegxr supports transparency ... works with IE10+ and is smaller than png!

##### **Quality**

* Always a bit of a compromise
* 75%, 85%, 90% compression is standard
* Some images can be pushed to 50% depending on the colour palette
* Perceptual Quality (Structured Similarity) algorithm
  - Looks at contrast/brightness
  - Returns value of how different they are
  - Humans can detect approx 10 million unique colours
  - Chroma Subsampling
    * chrome (colour)
    * luma (b&w)
    * 4:4:4 (Take 4 pixels in 8 pixel block)
    * 4:2:2 downsampling
    * 4:2:0 downsampling
    * Reduces the amount of colour in images and tricks the eye
    * Photoshop's _Save for Web_ does this automatically
    * Gimp offers Subsampling rates too

##### Lazy Loading

* Use JS to load images - create <img> and appendChild to DOM
* Onscroll, onload, load after critical content
* HTTP1 has a queue that just loads content
* HTTP2 just has everything being requested/downloaded at the same time and they compete for bandwidth - _load critical images first_


---

## Building and deploying microservices with the Serverless Framework

> Kevin Old (LifeWay)

#### Outline

1.
