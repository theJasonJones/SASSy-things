# SASSy-things
Similar to the LESS repo, only this time... Sass. Scss if you need to be technical. 

```scss
/*
*  Used to horizontally and vertically center an element. 
*  
*  Usage: @include center <- no arguments to use transforms to center elements.
*         @include center(w,h) <- if size is known.
*
*/

@mixin center($width: null, $height: null) {
  position: absolute;
  top: 50%;
  left: 50%;

  @if not $width and not $height {
    -ms-transform: translate(-50%, -50%);
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
  } @else if $width and $height {
    width: $width;
    height: $height;
    margin: -($width / 2) #{0 0} -($height / 2);
  } @else if not $height {
    width: $width;
    margin-left: -($width / 2);
    transform: translateY(-50%);
  } @else {
    height: $height;
    margin-top: -($height / 2);
    transform: translateX(-50%);
  }
}

/*
*  Used to place an transparent layer over an element, usually an image. (Note: must be wrapped in position: relative;). Utilizes the rgba() function.
*  
*  Usage: @include overlay(color value, decimal) 
*  color: Can be word or hex value 
*  opacity : Must be between 0 and 1 inclusive
*
*/

@mixin overlay( $bg-color : #000, $opacity : 0.5 ){ 
	background-color: rgba($bg-color, $opacity);
	height: 100%; 
	left: 0px; 
	position: absolute; 
	top: 0px; 
	width: 100%;  
	z-index: 2; 
}

/*
*  
*  Usage: @include bg-image(x, y) 
*  x : Can be top, left, bottom, right, center or a percent
*  y : Can be top, left, bottom, right, center or a percent
*
*/
@mixin bg-image($first-position : center, $second-position: center) {
  background-position: $first-position $second-position; 
  background-size: cover; 
  background-repeat: no-repeat;
}

/*
* Clearfix for badly behaving floats (Yes, some of us still support legacy IE)
* Source: http://nicolasgallagher.com/micro-clearfix-hack/
*
*  Usage: @extend %clearfix;
*
*/

%clearfix {
  *zoom: 1;
  &:before, &:after {
    content: " ";
    display: table;
  }
  &:after {
    clear: both;
  }
}

/*
*  Use rems for better, more responsive typography and fallback to pixels when rem isn’t supported.
*  
*  Usage: @include font-size(16px)
*
*/

@function calculateRem($size) {
  $remSize: $size / 16px;
  @return $remSize * 1rem;
}

@mixin font-size($size) {
  font-size: $size;
  font-size: calculateRem($size);
}

/*
* Turns on anti aliasing for smoother fonts on OSX
*
* Usage: @include font-smoothing(on);
*
* Argument can on or off. 
*
*/
@mixin font-smoothing($value: on) {
    @if $value == on {
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
    }
    @else {
        -webkit-font-smoothing: subpixel-antialiased;
        -moz-osx-font-smoothing: auto;
    }
}

// Everything below is credited to Andy.scss

/// Adds a thin black line above the element, and a thin gray line below the element, creating an inlay effect.
/// @access public
/// @example scss - Usage
///   .foo {
///     @include outline;
///   }
/// @example css - Result
///   .foo {
///     box-shadow: rgba(255, 255, 255, 0.5) 0 1px 0, inset rgba(0, 0, 0, 0.75) 0 1px 0;
///   }
@mixin outline {
    box-shadow: rgba(255, 255, 255, 0.5) 0 1px 0, inset rgba(0, 0, 0, 0.75) 0 1px 0;
}

/// Given the location of a webfont, will generate a font-face declaration with multiple file formats.
/// @access public
/// @param {String} $font-name - Font family name
/// @param {String} $file-name - File name (no extension)
/// @param {String | Number} $weight [normal] - Font weight
/// @param {String} $style [normal] - Font style
/// @example scss - Usage
///   @include font-face('gotham', '/fonts/gotham');
@mixin font-face($font-name, $file-name, $weight: normal, $style: normal) {
  @font-face {
    font-family: quote($font-name);
    src: url($file-name + '.eot');
    src: url($file-name + '.eot?#iefix')  format('embedded-opentype'),
         url($file-name + '.woff') format('woff'),
         url($file-name + '.ttf')  format('truetype'),
         url($file-name + '.svg##{$font-name}')  format('svg');
    font-weight: $weight;
    font-style: $style;
  }
}

/// Retina image media query helper; given an image path with a 2x-sized version of an image, will load that image as a background-image on high-resolution devices.
/// @access public
/// @param {String} $image - Image path
/// @param {Length} $width - Image width
/// @param {Height} $height - Image height
/// @example scss - Usage
///   .foo {
///     @include image-2x('../images/image.png', 100%, auto);
///   }
@mixin image-2x($image, $width, $height) {
    @media (-webkit-min-device-pixel-ratio: 1.3),
           (min-resolution: 124dpi),
           (min-resolution: 1.3dppx) {
        /* on retina, use image that's scaled by 2 */
        background-image: url($image);
        background-size: $width $height;
    }
}

/// Background gradient helper
/// @access public
/// @param {Color} $start-color - Start color
/// @param {Color} $end-color - End color
/// @param {String} $orientation - Type of gradient, either `vertical`, `horizontal` or `radial`
/// @example scss - Usage
///   .foo {
///     @include background-gradient(red, black, 'vertical');
///   }
/// @example css - Result
///   .foo {
///     background: -webkit-linear-gradient(top, red, black);
///     background: linear-gradient(to bottom, red, black);
///   }
@mixin background-gradient($start-color, $end-color, $orientation) {
    background: $start-color;

    @if $orientation == 'vertical' {
      background: -webkit-linear-gradient(top, $start-color, $end-color);
      background: linear-gradient(to bottom, $start-color, $end-color);
    } @else if $orientation == 'horizontal' {
      background: -webkit-linear-gradient(left, $start-color, $end-color);
      background: linear-gradient(to right, $start-color, $end-color);
    } @else {
      background: -webkit-radial-gradient(center, ellipse cover, $start-color, $end-color);
      background: radial-gradient(ellipse at center, $start-color, $end-color);
    }
}

/// Shorthandizes position declarations.
/// @access public
/// @param {String} $type - Either `relative`, `absolute` or `fixed`
/// @param {Length} $left [null] - Left offset
/// @param {Length} $right [null] - Right offset
/// @param {Length} $top [null] - Top offset
/// @param {Length} $bottom [null] - Bottom offset
/// @example scss - Usage
///   .foo {
///     @include position(absolute, $top: 10px, $left: 10px);
///   }
/// @example css - Result
///   .foo {
///     position: absolute;
///     left: 10px;
///     top: 10px;
///   }
@mixin position($type, $top: null, $right: null, $bottom: null, $left: null) {
    position: $type;
    top: $top;
    right: $right;
    bottom: $bottom;
    left: $left;
}

```
