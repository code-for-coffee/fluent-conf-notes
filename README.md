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

#### Who is doing this?

* 100% of sampled sites have 1+ image
  * Median page has 55 images per page
* 89% of pages have at least one jpeg
  * Median of 20-25 jepgs per page
* 10% of pages have at least 1 wepb
  * Median 1-5 webp images per page
* 30% of pages have at least 1 svg
  * Median of 1-5 svg images per page (likely due to sprite sheets or just icons)

##### Adoption

* 0.2% of sites have at least 1 srcset
* 2% of sites use the picture tag


#### Service Workers
* Built in browser proxy
* Javascript based
* Decent support (no Safari yet)
* Bonus of push notifications
* Cache assets on start up
* Use cache when no network (serve content to users when their devices are offline)
* Error handle when no cache and no network

#### Why you should care?
* JS loading - execution time of javascript load
  * Scripts are going to load syncronously
  * We can use async which will load multiple javascript files together - gained some time by combining
  * defer - to grab javascript file after others have loaded
  * add into script tag async or defer
  * with async you cannot guarantee order, must combine files
  * performance.measure
  * performance.mark
  * The Performance interface represents timing-related performance information for the given page.

> 43% sites use async - 2 scripts per page

* 14% sites use defer
* 71% - 1 script per page

#### User Timings
* Report metrics back to business to see how it’s performing
* 0.19% of sites use performance.mark
* 40% 2 timings
* 116 sites with service workers on their sites 0.1% adoption is low
* User timing helps measure what’s important
* Service workers are not well adopted. Can provide offline experiences

#### HTTPS & HTTP/2
Encrpytion and Sharing of secrets between the client and the server so any information passing between the two remains secret.
* Security is not friendly with performance
* Https everywhere
* All traffic secure
* Only deliver brotli and service workers or geolocation over https
* istlsfastyet.com
* TLS has exactly one performance problem: it’s not widely used enough
* TLS handlshake
* TLS sesison resumption
* TLS false startx

#### TLS 1.3 & 0-RTT

* Full Handshake
* Transport Layer Security (TLS)
* Predecessor is Secure Sockets Layer (SSL)
* 2nd RTI   - TWO ROUND TRIPS
* Session Resumption. Going to a server that it’s been before
* 0 RTI - ONE ROUND TRIP
* ssllabs.com

#### HTTP2

* H2 Recap
* Multiplexing
* Header Compression
* Server Push
* Multiplexing is a method in HTTP/2 by which multiple HTTP requests can be sent and responses can be received asynchronously via a single TCP connection. Multiplexing is the heart of HTTP/2 protocol.
* uses binary framing
* We can stream multiple things at the same time and the client is clever enough to put it all back together

##### HPACK  
- compress header data. Sending line reference to line number rather than entire header
* H2 can push resources 1-1 relationship between making a request and receiving a file.
* Server push lets the server preemptively “push” website assets to the client without the user having explicitly asked for them.

If you don’t have TLS connection you have poor performance for everyone
Certificate needs verifiying. Going off to their CLR and making a request to make sure the request is still valid. 1.5x increase in performance on https. Up to 30% improve on page load when using h2. 15% improvement h2

#### Implementation of HTTPS
* Choose a certificate provider - Symantec LEtsEncrypt
* Choose a verification type - EV OV DV
* Choose a certificate type - Single host SAN Wildcard
* Server / CDN
  * If using linux a tool like certbot can help
  * mixed access or HSTS
  * CDN’s vary widely


#### Problems with Push
* repeat users
* browser implementation
* server side logic
* Push from the edge of the network
* Push from the origin
* https://canipush.com
* https://shouldipush.com

#### HTTPS ADOPTION
* 31% of all websites adopted - fully secure
* 35-40% is actuallly secure content on all sites

#### h2 adoption
* 4%
* measure and optimize
* rewards for moving to https: h2 brotili, service workers
* h2 can give a instant performance boost

#### Resource Hints
* dns-prefetch
* preconnect
* preload

#### Push alternative - POST HTML

`<link rel=preload>`

Pros:
* Add “as” for download priority
* Proper accept headers
* Content-security-policy
* Honours Cache
* Can Load Asyncronously
* Add media queries for responsive loading
* Can Load different domains

Cons:
* Still requires HTML to be sent
* Still requires the HTML to be processed

5% use dns-prefetch
30% -  4 domains prefetched

1% of sites use pre-connect
18% 4 domains connected

0.6% of sites use preload
27%

#### Summary
* Good way of optimising network utilisation
* DNS-prefetch the most popular
* Overall Adoption is low

---

## Building and deploying microservices with the Serverless Framework

> Kevin Old (LifeWay)

- https://github.com/kevinold/serverless-tutorial-fluentconf-2017 (source)

#### Objecives of 'Serverless' Architectures

1. Write single purpose stateless functions
2. DEsign push-based, event-driven pipelines
* Create thicker, more powerful front-ends
* Embrace 3rd party services
* Use a compute service to execute code on demand

#### AWS Lambda

- Really cool but a massive pain to setup
- You don't manage the infrastructure
- Serverles removes the AWS GUI and lets you just code this


#### Serverless 101

- _functions_ are triggered by...
- _Events_
- Resource (S3, dynamo, IAM, etc)
- Service
- Deployed to AWS East

#### Quick commands

- `npm install -g serverless`
- `serverless create -t aws-nodejs`
- `serverless deploy`
  * `serverless deploy -v`
  * `serverless deploy --stage stagehouse`
- `serverless remove`

#### Deployment

```bash
$ serverless deploy -v
Serverless: Packaging service...
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
CloudFormation - CREATE_IN_PROGRESS - AWS::CloudFormation::Stack - hello-world-dev
CloudFormation - CREATE_IN_PROGRESS - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_IN_PROGRESS - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_COMPLETE - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_COMPLETE - AWS::CloudFormation::Stack - hello-world-dev
Serverless: Stack create finished...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service .zip file to S3 (5.3 KB)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
CloudFormation - UPDATE_IN_PROGRESS - AWS::CloudFormation::Stack - hello-world-dev
CloudFormation - CREATE_IN_PROGRESS - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_IN_PROGRESS - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_IN_PROGRESS - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_COMPLETE - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_IN_PROGRESS - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_COMPLETE - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_COMPLETE - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Version - HelloLambdaVersionwYourTokenKeyHere
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Version - HelloLambdaVersionwYourTokenKeyHere
CloudFormation - CREATE_COMPLETE - AWS::Lambda::Version - HelloLambdaVersionwYourTokenKeyHere
CloudFormation - UPDATE_COMPLETE_CLEANUP_IN_PROGRESS - AWS::CloudFormation::Stack - hello-world-dev
CloudFormation - UPDATE_COMPLETE - AWS::CloudFormation::Stack - hello-world-dev
Serverless: Stack update finished...
Service Information
service: hello-world
stage: dev
region: us-east-1
api keys:
  None
endpoints:
  None
functions:
  hello: hello-world-dev-hello

Stack Outputs
HelloLambdaFunctionQualifiedArn: arn:aws:lambda:us-east-1:SomePlaceNumber:function:hello-world-dev-hello:1
ServerlessDeploymentBucketName: hello-world-dev-serverlessdeploymentbucket-i5nv4sve3o8d
```
