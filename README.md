# A More Configurable Twitter Cards Template For Hugo

If you need more control over the appearance and values of the Twitter Cards generated by your Hugo website, this template may be what you are looking for.

These template is designed to work with Hugo versions 0.54.x and 0.55.x. It will likely work with other versions, but has not been tested.

The [default Hugo template](https://github.com/gohugoio/hugo/blob/master/tpl/tplimpl/embedded/templates/twitter_cards.html) for Twitter Cards pulls values from the page and configuration settings to create the card. Unfortunately, this doesn't always produce the ideal Twitter Card. This template gives you more control of the Twitter Card creation by allowing you to define default values for the cards in the site configuration, and also allows you define explicit values for a card in the page front matter. In absense of specifically defined values, the template pulls the values for the card from the same locations as the default Hugo template.

So when it comes to Twitter Card generation, the following logic is followed for creating the card and populating the values of the card:

1. First, determine if a Twitter Card should be generated at all. If the Twutter Card is not to be generated, then we're done. Output no Twitter Card **\<meta\>** tags.
    1. By default Twitter Cards should be generated.
    2. However, a site wide configuration setting can turn off generation for the entire site.
    3. But the settings in an individual page's front matter can override the site wide and default settings.
2. But if the Twitter Card is to be generated, then the following Twitter Card values need configurable in both the site configuration and the individual page front matter.
    1. Twitter Card type
    2. Twitter Card image
    3. Twitter Card image alternate text
    4. Twitter Card title
    5. Twitter Card description
    6. Twitter Card creator
    7. Twitter Card site
3. For Twitter Card values the following precedence should be followed.
    1. If defined, a page's front matter values for the Twitter Card take the highest precedence and should be used above all others.
    2. If not defined, then the page's resources and values should be used. This is like the behavior of the default Hugo Twitter Card template.
    3. If a value is still not set, then use the site's Twitter Card configuration values, if they are defined.
    4. Finally, the template default values should be used if a Twitter Card value is still not defined. These template defaults might include values from the page's front matter and the site's non-Twitter Card configuration settings, as appropriate. This is like the behavior of the default Hugo Twitter Card template.
4. At the end of determining values, if any Twitter Card value is set to false, then the **\<meta\>** tag for that value should not be generated.
5. Also, if the Twitter Card type is set to false, then the entire Twitter Card should not be generated. Output no Twitter Card **\<meta\>** tags.
6. And if the Twitter Card image is set to false, then the Twitter Card image alternate text **\<meta\>** should not be generated, even if the alternate image text contains a non false value.

For a more detailed writeup of how this template works, see this [post](https://dereckcurry.com/posts/more-flexible-twitter-cards-in-hugo/) on my personal [blog](https://dereckcurry.com/posts/).


## Template Installation

To use this new template, you will first need to install the template (configurable_twitter_cards.html) in your layouts directory. I chose to put mine in the partials subdirectory.

```
root
    layouts
        partials
            configurable_twitter_cards.html
```

## Theme Layout Changes

Next, you will need to locate where the existing Twitter Card template is being called in your theme. You will then need to copy that file to the corresponding location in your layouts directory. For the theme that I am using, the template was being called from the baseof.html file.

```
root
    layouts
        _default
            baseof.html
```

After copying the file to your layouts directory, you will need to modify it to call your new template. For the theme I'm using it meant the following code had to be changed:

```html
<!DOCTYPE html>
<html lang="{{ .Site.LanguageCode }}">
    <head>
        ... Template Head stuff removed for clarity ...

        {{ template "_internal/twitter_cards.html" . }}

        ... Lots of template Head stuff removed for clarity ...
    </head>
    <body>
        ... Template Body stuff removed for clarity ...
    </body>
</html>
```

The template line was changed to:

```html
<!DOCTYPE html>
<html lang="{{ .Site.LanguageCode }}">
    <head>
        ... Template Head stuff removed for clarity ...

        {{ partial "configurable_twitter_cards.html" . }}

        ... Lots of template Head stuff removed for clarity ...
    </head>
    <body>
        ... Template Body stuff removed for clarity ...
    </body>
</html>
```

Only one line of code had to be changed to call this new template on my site. Your site theme may have a different layout and you may need to make the change to call the partial in different or multiple places.


## How To Use

After making the layout changes, when you compile your site the new flexible Twitter Cards template should be used. It should continue to work as the default Hugo Twitter Cards Template. To take advantage of the flexibility of this new template, you'll need to make changes in either the site configuration or the front matter of a page.


### Site Configuration

To set default Twitter Card values for you entire site, add the following to your site config file. I'm using TOML, so modify as necessary if you are using a different configuration format.

```
[params.twitterCard]
    generate = true
    card = "summary"
    image = "images/twitter-card-summary-image.jpg"
    imageAlt = "Default Twitter Card Image"
    site = "your_twitter_handle"
    creator = "your_twitter_handle"
    title = "Your Site Title"
    description = "Your site description."
```

Most of these values should be fairly self explanatory as they mostly follow the conventions for naming in the actual [Twitter Cards](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) themselves.

The only one that is new is the generate setting. Setting the generate param value to false will keep all twitter cards for generating for the site. If the generate parameter is not set, then Twitter Cards will be created by default. So you should only need to define and set the generate param value if you want to turn off Twitter Card generation.

In practical use, you probably won't need to use all these params and values in your site configuration. If they aren't defined in the site configuration, then the precedence rules listed above will determine the values to use for the Twitter Cards. Setting a param value to false in these settings will keep the corresponding Twitter Card **\<meta\>** tag from being generated, unless overruled by the precedence rules.


### Page Front Matter

Twitter Card values can also be specified in the front matter of a page. The front matter params follow the same naming conventions of the site configuration params. The example below is in TOML, so you modify as necessary for the front matter configuration type your site uses.

```
+++
[twitterCard]
    generate = true
    card = "summary"
    image = "images/twitter-card-summary-image.jpg"
    imageAlt = "Default Twitter Card Image"
    site = "your_twitter_handle"
    creator = "your_twitter_handle"
    title = "Your Page Title"
    description = "Your Page Description"
+++
```

Again, most of these values should be fairly self explanatory as they mostly follow the conventions for naming in the actual [Twitter Cards](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/overview/abouts-cards) themselves.

Also, you probably won't need to set most of these params for a page, as the values will be defined by the precedence rules listed above. But the final value to be used for each of these params can be defined in the front matter of each page. Setting a param to false will keep the corresponding Twitter Card **\<meta\>** tag from being generated.

Setting generate to false will keep the Twitter Card for the page from being generated. By default, Twitter Cards will be created for all pages unless the site configuration has set generate to false, or if a page has set generate to false. The generate value defined for a page will take precedence over the site configuration.


## Conclusion

This is my first attempt at using Hugo template languages, so please let me know if I have done something wrong. I've tested these locally and with my personal [site](https://dereckcurry.com) and haven't found any issues. But please let me know if you discover any problems.