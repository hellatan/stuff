#SCSS and other css-related methodolgies used at 1stdibs

## bourbon "api"
1. What is bourbon? 
    - "A simple and lightweight mixin library for Sass."
2. Why bourbon?  
    - http://bourbon.io/

## 1stdibs-specific mixins and functions
1. Where are they? - /dibs/assets/scss/global/_mixins_and_fns.scss
2. What's in here?
    - Functions
        - @function grid-calculator($columns, $width, $base-width:$base-width, $nopadding:false) 
        - @function divide-values($top, $bottom) 
        - @function calc-percentage($value) 
        - @function create-spacing-value($output, $value) 
    - Mixins
        - @mixin border-radius($radii) 
        - @mixin gridifier($columns, $width:100) 
        - @mixin media-query($max-width: -1, $min-width: -1) 
        - @mixin ratio-spacing($type, $output, $context, $target...) 
        - @mixin moz-specific-css()
        - @mixin ellipsis($no-wrap: true)s
        - @mixin opacity($opacity)
        - @mixin hidpi_social($image, $width:0, $height:0, $type:png)

## smacss methodology
1. What is SMACSS?
    - Acronym: Scalable and Modular Architecture for CSS
2. What's it good for?
    - As the name says, modularity 
    - Reducing CSS specificity
3. How do you approach this?
    - Think of specific areas in the page as their own area aka a module
    - The module should have one base name aka `class="module"`
    - As you create new layers of DOM elements within the module, the base name would be a prefix

Let's take this html structure as an example:

    <li class="product l-col-4-full">
        <div class="product-inner">
            <a href="/furniture/folk-art/weathervanes/cow-weathervane/id-f_844669/" class="product-link has-image">
                <img src="//shard3.1stdibs.us.com/assets/images/global/blank.gif" width="240" height="240" data-original="//shard3.1stdibs.us.com/archivesE/upload/8759/29_13/844669/844669_l_s.jpg" class="lazy product-image" alt="Cow Weathervane" />
           </a>
            <noscript>
                <img src="//shard3.1stdibs.us.com/archivesE/upload/8759/29_13/844669/844669_l_s.jpg" class="lazy product-image" alt="Cow Weathervane" width="240" height="240" />
            </noscript>
            <div class="product-name">
                <a href="/furniture/folk-art/weathervanes/cow-weathervane/id-f_844669/" class="product-link">
                    <span class="product-link-name">Cow Weathervane</span>
                </a>
            </div>
            <div class="product-dimensions cf">
                <div class="product-dimension">
                    L 28 in. H 17.25 in.
                </div>
            </div>
            <span class="product-price is-invisible">
                <a href="#view-price" class="product-price-link" data-tracking-action="view price" data-tracking-position="view price link - search or browse page" data-price="$13,500">View Price</a>
            </span>
            <div class="product-seller">
                <a href="/dealeritems.php?sellerID=*BB002BD5F9A78B69160AE27E48D292E" class="seller-logo-link">
                    <img src="//shard3.1stdibs.us.com/assets/images/global/blank.gif" width="150" height="40" data-original="//shard4.1stdibs.us.com/dealer_images/8759/jtillouscrolllogo.gif" class="lazy seller-logo-image" alt="Jeffrey Tillou Antiques" />
                </a>
                <noscript>
                    <img src="//shard4.1stdibs.us.com/dealer_images/8759/jtillouscrolllogo.gif" class="lazy seller-logo-image" alt="Jeffrey Tillou Antiques" />
                </noscript>
            </div>
        </div>
    </li>