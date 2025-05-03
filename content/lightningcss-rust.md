+++
title = "Lightning CSS with Rust"
date = 2025-01-26
draft = false

[taxonomies]
categories = ["Software"]
tags = ["rust", "web"]

[extra]
toc = true
keywords = "CSS lightningcss rust bundler minifier script web development"
+++

Lightning CSS ⚡ is a way to minify CSS files and ensure backwards compatibility with a range of browsers. This blog post will demonstrate how to use Lightning CSS in Rust to manipulate CSS files.

This blog post will describe how to read a CSS file from disk then bundle, minify and compile it for a range of different CSS versions.

## Installing Lightning CSS

We need to first install lightningcss into our Rust project, as it is still beta at the time of writing - the installing process is a bit different.
```toml
[dependencies]
lightningcss = "^1.0.0-alpha.65"
```
_Make sure to check the [latest version](https://lib.rs/crates/lightningcss/versions) before installing_

Then the main items we need to install are:
```rust
use lightningcss::{
    bundler::{self, FileProvider},
    printer::PrinterOptions,
    stylesheet::{MinifyOptions, ParserOptions},
};
```
All these options all us to interact with our CSS file and modify it correctly.

## Reading a CSS file
Make sure that `fs` is installed first:
```rust
use std::fs;
```
Then we can read the CSS file from disk:
```rust
fn bundle_css() {
    let fs = FileProvider::new();
    let mut bundler = bundler::Bundler::new(&fs, None, ParserOptions::default());
    let mut stylesheet = bundler
        .bundle(Path::new(&css_path))
        .expect("Failed to bundle css");
}
```
By loading it into the bundler, we are able to combine a range of disparate CSS files into a single file.

To minify the CSS we can adjust the `MinifyOptions` through the `minify` command:
```rust
stylesheet
        .minify(MinifyOptions::default())
        .expect("Failed to minify css");
```
This will try to minify the CSS to reduce the size of the CSS file reducing the load on your user's browsers.

Minification is a two stage process, now we need to "print" the CS file which will "crystallise" the minification:
```rust
let res = stylesheet
        .to_css(PrinterOptions {
            minify: true,
            targets: get_browser_targets(),
            ..Default::default()
        })
        .expect("Failed to write css to file");
```

Then we can write the CSS to a file:
```rust
fs::write(css_path, res.code).expect("Failed to write css to file");
```

This will   

### Results
This can turn CSS like this:
```css
body {
  font-family: "Fira Sans", Helvetica, Arial, sans-serif;
  background-color: white;
  font-size: 1.5rem;
  line-height: 1.6;
  /* Ensure the footer is always at the bottom of the screen */
  min-height: 100vh;
  display: flex;
  flex-direction: column; 
}
```
into this:
```css
body {
  background-color: #fff;
  flex-direction: column;
  min-height: 100vh;
  font-family: Fira Sans, Helvetica, Arial, sans-serif;
  font-size: 1.5rem;
  line-height: 1.6;
  display: flex;
}
```
_* code taken from [rust-lang.org](https://www.rust-lang.org/)_

It may not look like much, but it is cutting down on a character here or there, and when you have a large chunk of CSS, it can save a lot.

Or you can even minify it into this:
```css
body{background-color:#fff;flex-direction:column;min-height:100vh;font-family:Fira Sans,Helvetica,Arial,sans-serif;font-size:1.5rem;line-height:1.6;display:flex}
```
#### Working example
A working example of this can be found here on my GitHub: <https://github.com/Hysterelius/lightningcss_rust>, it's under the *Unlicense* license, so feel free to do whatever you want with it.


### p.s. Targetting different browsers
One of the biggest features of using LightningCSS is being able to target different browsers and versions, and transpile the CSS to work for them, so that your site is more accessible to a wider audience.

This is done by specifying browsers by their *major*.*minor*.*patch* version, so for example:
```rust
Browsers {
    android: Some(131 << 16),  // Android 131
    chrome: Some(103 << 16),   // Chrome 103
    edge: Some(127 << 16),     // Edge 127
    firefox: Some(115 << 16),  // Firefox 115
    ie: Some(11 << 16),        // IE 11
    ios_saf: Some(11 << 16),   // iOS Safari 11
    opera: Some(80 << 16),     // Opera 80
    safari: Some(15 << 16),    // Safari 15
    samsung: Some(25 << 16),   // Samsung Internet 25
}
```
This selects browsers by their major versions, but these are a pain to write - so we can use `from_browserslist()` to convert a [browserslist](https://browsersl.ist/) string into a `Browsers` object:

Ensure that you have the `browserslist` feature enabled in your `Cargo.toml` with `cargo add lightningcss --features browserslist`, and then you can use it like this:
```rust
let browsers: Targets = Browsers::from_browserslist(vec![">0.1%"])
        .expect("Failed to parse browserslist")
        .unwrap()
        .into();
```
We convert it into a `Targets` object which is used in both the minification and printing stages. We can put the browserslist strings in the `vec`, which is the strings from the <https://browsersl.ist/> site.

* By targetting `>0.1%` we are targetting all browsers with a market share of over 0.1%, which is a good starting point and covers over 93.3% of users.

Then we adjust our code to be like this:
```rust
let browsers: Targets = Browsers::from_browserslist(vec![">0.1%"])
        .expect("Failed to parse browserslist")
        .unwrap()
        .into();

    stylesheet
        .minify(MinifyOptions {
            targets: browsers,
            ..Default::default()
        })
        .expect("Failed to minify css");

    let res = stylesheet
        .to_css(PrinterOptions {
            targets: browsers,
            // minify: true,
            ..Default::default()
        })
        .expect("Failed to write css to file");
```
This will then minify the CSS to work for the browsers specified in the `browserslist` string.

## Conclusion
Through LightningCSS we can ensure that we create CSS code and file which are accessible to a wider audience, and 