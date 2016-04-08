jdf-ngThemeSwitcher
===================

Theme (stylesheet) switcher directive for AngularJS


Usage
=====

Include the minified JS file in your HTML. It is recommended (but not required) to use the ngStorage module to store the user's selected template in localStorage for persistence.

```html
<script src="jdf-ngThemeSwitcher.min.js"></script>
<script src="ngStorage.min.js"></script>
```

Include the module as a dependency in your app.

```js
app.module('myApp', [
  'jdf.ngThemeSwitcher',
  'ngStorage'
]);
```

Include the theme-switcher tag inside the hierarchy of an attached controller that will handle the switching.

```html
<div ng-controller="MyController">
  <theme-switcher urls="[theme.url]"></theme-switcher>
</div>
```

And include a list, dropdown, etc. to provide the switching functionality. This could be a dropdown in a bootstrap nav bar, a ui-bootstrap dropdown, or whatever. A list is shown here for simplicity's sake.

```html
<ul role="menu">
  <li ng-repeat="theme in themes"><a href ng-click="setTheme(theme)">{{theme.name}}</a></li>
</ul>
```

In the controller, include a method for switching the stylesheet.

```js
(function() {
  function MyController($scope, $localStorage) {
    // Bootswatch themes
    $scope.themes = [
      { name: "Amelia", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/amelia/bootstrap.min.css"},
      { name: "Cerulean", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/cerulean/bootstrap.min.css"},
      { name: "Cosmo", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/cosmo/bootstrap.min.css"},
      { name: "Cyborg", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/cyborg/bootstrap.min.css"},
      { name: "Flatly", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/flatly/bootstrap.min.css"},
      { name: "Journal", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/journal/bootstrap.min.css"},
      { name: "Readable", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/readable/bootstrap.min.css"},
      { name: "Simplex", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/simplex/bootstrap.min.css"},
      { name: "Slate", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/slate/bootstrap.min.css"},
      { name: "Spacelab", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/spacelab/bootstrap.min.css"},
      { name: "United", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/united/bootstrap.min.css"},
      { name: "Yeti", url: "//maxcdn.bootstrapcdn.com/bootswatch/3.2.0/yeti/bootstrap.min.css"}
    ];

    // initialize localStorage with default theme
    $scope.$storage = $localStorage.$default({
      theme: {
        name: $scope.themes[6].name,
    	url: $scope.themes[6].url
      }
    });

    // the theme switching method
    $scope.setTheme = function(theme) {
      // don't do anything if the theme is the same
      if (theme.name !== $scope.theme.name) {
        // set the model so the directive updates
        $scope.theme = theme;
        // save the new theme to localStorage
        $scope.$storage.theme = theme;
      }
    }

    // initialize the theme - pull from localStorage (which gets the default if none is set)
    $scope.theme = $scope.$storage.theme;
  }
  app.controller('MyController', ['$scope', '$localStorage', MyController]);
})();
```

Full plunkr example: http://plnkr.co/edit/hNs7ps


Multiple Stylesheets Per Theme
==============================

This theme switcher was built with the idea in mind that you may want use multiple stylesheets for a single theme. This could be used for having different base styles per device extended with visual styles in another theme stylesheet, for using standardized stylesheets (like Bootswatch) and adding in a custom stylesheet to extend them, or a variety of other reasons.

The urls attribute on the theme-switcher element takes an array of urls. Instead of the array literal used in the above example, an array model can be substituted.

For the sake of brevity, I will describe only the differences between the code for a single stylesheet (above) and that needed for multiple stylesheets.

The only change in the HTML is the model for the theme-switcher.

```html
<theme-switcher urls="theme.urls"></theme-switcher>
```

The themes array and controller logic needs changed just a bit.

```js
$scope.themes = [
  { name: "Theme A", urls: ['theme_a_1.css', 'theme_a_2.css'] },
  { name: "Theme B", urls: ['theme_b_1.css', 'theme_b_2.css'] }
];

// initialize localStorage with default theme
$scope.$storage = $localStorage.$default({
  theme: {
    name: $scope.themes[0].name,
    urls: $scope.themes[0].urls
  }
});
```

Otherwise everything remains the same. The directive will create a link rel=stylesheet element for each stylesheet in the array.

Full plunkr example: http://plnkr.co/edit/r8ejRE

Known Issues
============
~~Chrome (at least - help me test on other browsers and platforms) doesn't always repaint when the stylesheet is switched. Sometimes you need to move the mouse to cause the new styles to take affect. If anyone knows of any workarounds to fix this behavior while still keeping the directive clean, let me know. I haven't tested inserting an empty DOM element to trigger a reflow. That may work, but isn't the most elegant solution.~~
It appears this is no longer an issue on recent Chrome updates.
