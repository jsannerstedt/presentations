title: Image optimizations
author:
  name: Joel Sannerstedt
  url: http://jsannerstedt.github.io/
output: images.html
controls: false
style: ../main.css

--

## Background
- cms heavy site with loads of images
- lots of articles
- preact

--

## Problems
- jumping text
- editors
- payload
- simultaneous request

--

### Problem - Jumping text
- text jumps around when images load, if no space is reserved
[example](examples/images/spacing.html)

--

### Solution - reserve space
- pre defined ratios 4x3 16x9 21x9 etc
- when designing elements, use desired ratio
[example](examples/images/reserve-space.html)

--

#### code

```css
img {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}
.image-holder {
    position: relative;
}
.ratio-16x9 {
    padding-top: 56.25%;
}
.ratio-3x1 {
    padding-top: 33.33333%;
}
```

--
### Problem - editors
- editors add various formats, sizes and ratios

--

### Solution - calculate ratio
- find the closest matching ratio, use that and resize the image
[example](https://coop.no/aktiv/styrke/derfor-er-kosthold-sa-viktig-for-treningen)

--

### Problem - payload
- sending larger image than we need

--

### Solution - srcset
- serve different images based on resolution
- calculate the sizes based on ration and predefined breakpoints
[example](examples/images/srcset.html)

-- 

#### code

```html
<img 
    alt="image" 
    srcset="what-we-do.jpg 992w, 
    what-we-do-small.jpg 480w">
```

--

### Problem - responsive design vs size
- column layouts, needs different image size

--

### Solution - sizes
- use sizes attribute
[example](examples/images/sizes.html)

--

#### code

```html
<img class="responsive"
     alt="image"
     srcset="what-we-do.jpg 992w, 
             what-we-do-small.jpg 480w"
     sizes="(min-width: 800px) 50vw, 100vw"/>
```

--
### Problem - images below the fold
- loading images that aren't in the viewport
[example](examples/images/multiple-images.html)

--

### Solution - lazy loading
- intersectionObserver
[example](examples/images/intersectionObserver.html)

#### code

```javascript
const observer = new IntersectionObserver(
  (entries, observer) => {
      entries.forEach(e => {
        if (e.intersectionRatio > 0) {
          e.target.src = e.target.dataset.src;
          observer.unobserve(e.target);
        }
      })
    }, {});
Array.from(document.getElementsByTagName('img'))
  .forEach(e => observer.observe(e));
```

--