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




---

## Building and deploying microservices with the Serverless Framework

> Kevin Old (LifeWay)

#### Outline

1.
