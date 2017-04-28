# Complete automated analytics integration for Meteor
OK GROW! analytics uses a combination of the browser [History API](https://developer.mozilla.org/en-US/docs/Web/API/History), Meteor's [accounts](https://guide.meteor.com/accounts.html) package and Segment.io's [analytics.js](https://segment.com/docs/libraries/analytics.js/) to automatically record and send user identity and page view event data from your Meteor app to your analytics platforms.

In version 3.X of _this_ package, the automatic page view tracking is handled by our new _router-agnostic_ [`@okgrow/auto-analytics`](https://www.npmjs.com/package/@okgrow/auto-analytics) NPM package, which can be used by _any_ JavaScript application whether using Meteor or not. _This_ package adds automatic user identification by using hooks in the Meteor accounts package and building on Segment.io's `analytics` package _through_ the `@okgrow/auto-analytics` package.

## Analytics 2.1.0+
Our Analytics package has been rewritten to be router agnostic. You should be able to use this package with any router that you use with Meteor app. We have tested and used our Analytics package with iron-router, flow-router, and react-router. You can view and test this out in our iron-router, flow-router and react-router example apps located in the `examples` folder.

**NOTE**: A fundamental change that may affect some applications is that we no longer look for or use the router's route name when logging page views. Instead we use `document.title`. This may affect applications that do not change or set their `document.title` for each screen or page of their application. The simplest solution is to simply set document title like this `document.title = "My new title";` for each screen or page in your application. If you are using `flow router` or `iron router` you can remain at `okgrow:analytics@2.0.1` to keep using the `name` of the route for your analytic events.

### Pre Meteor 1.3.1
For Meteor Apps older than v1.3.1, please use [v1.0.9](https://github.com/okgrow/analytics/releases/tag/v1.0.9) of this package. Going forward this package will officially only be supporting Meteor Apps >= v1.3.1


## Installation

`> meteor add okgrow:analytics`


## Currently Supported Analytic Services
See the [`@okgrow/auto-analytics`](https://www.npmjs.com/package/@okgrow/auto-analytics) package for up-to-date details of supported analytics services.

## Ad-blocker
When running your Meteor app in "development mode" ad-blocking web-browser extensions may block the `okgrow:analytics` package due to the word "analytics" in the package name. This only occurs when running Meteor in "development mode" because files are not bundled together and minified. To work around this issue you can disable your ad-blocker when running in development mode.

To test that application with an ad-blocker, run your Meteor app in production mode with this command:

`meteor run --production --settings settings.json`

**NOTE** If an ad-blocker is enabled the expected behavior is that analytic events will not be received. You'll see an error message in your console reporting the events being blocked.


## Configuration
This package will automatically configure the underlying `@okgrow/auto-analytics` package using `Meteor.settings.public.analyticsSettings`. In Meteor you typically specify your settings using a `settings.json` file:

```
{
  "public": {
    "analyticsSettings": {
      // Add your analytics tracking ids here (remove this line before running)
      "Google Analytics" : {"trackingId": "Your tracking ID"},
      "Amplitude"        : {"apiKey": "..."},
      "Chartbeat"        : {"uid": "..."},
      "comScore"         : {"c2": "..."},
      "HubSpot"          : {"portalId": "..."},
      "Intercom"         : {"appId": "..."},
      "Keen IO"          : {"projectId": "...", "writeKey": "..."},
      "KISSmetrics"      : {"apiKey": "..."},
      "Mixpanel"         : {"token":  "...", "people": true},
      "Quantcast"        : {"pCode": "..."},
      "Segment.io"       : {"apiKey": "..."}
    }
  }
}
```

And run your app with that settings file as follows:

`meteor --settings settings.json`

See the [`@okgrow/auto-analytics`](https://www.npmjs.com/package/@okgrow/auto-analytics) package for more details on configuration.


### Page views
See the [`@okgrow/auto-analytics`](https://www.npmjs.com/package/@okgrow/auto-analytics) package for details on page view tracking. In short, that package uses the browser [History API](https://developer.mozilla.org/en-US/docs/Web/API/History) to automatically track page views.

Since the History API is used to automatically track page views, `document.title` is used instead of the router's route name as the default page name.

If you rely on your router's route name for the page name in page view events, you can easily set `document.title` programming using the router's route name. Here are examples of how to do this with React Router, Flow Router and Iron Router:

#### React Router

In your router setup, add a name property to your routes:
```
<Router history={ browserHistory }>
  <Route path="/" name="Home" component={ App } />
  <Route path="/one" name="One" component={ App } />
  <Route path="/two" name="Two" component={ App } />
  <Route path="/three" name="Three" component={ App } />
</Router>
```
**NOTE** The current route is passed in as a property named `route` to your component.

Then, in the `render()` function of your main layout component, using a package like [`react-document-title`](https://github.com/gaearon/react-document-title):
```
render() {
  return (
    <DocumentTitle title={this.props.route.name}>
      ...
    </DocumentTitle>
  );
}
```


#### Flow Router
```
Template.mainLayout.onRendered(function() {
  Tracker.autorun(() => {
    document.title = FlowRouter.getRouteName();
  });
});
```

#### Iron Router
```
Template.mainLayout.onRendered(function() {
  Tracker.autorun(() => {
    document.title = Router.current().route.getName();
  });
});
```

#### Disabling automatic page views
To disable automatic page view tracking change `Meteor.settings` as shown below then manually log a page view by calling `analytics.page('page name')`:

```
{
  "public": {
    "analyticsSettings": {
      // Disable autorun if you do not want analytics running on every route (remove this line before running)
      "autorun"  : false
    }
  }
}
```


### Log signin/signout
If you have the `accounts` package installed, this package will automatically track when a user logs in and logs out. Logging in will call `identify` on the user and associate their `Meteor.userId` to their previous anonymous activities.


### Event tracking
See the [`@okgrow/auto-analytics`](https://www.npmjs.com/package/@okgrow/auto-analytics) package for details on event tracking. In short, track any event by calling the `analytics.track()` function:
```
analytics.track("Bought Ticket", {
  eventName: "Wine Tasting",
  couponValue: 50,
});
```


### Track visitor scrolling
Josh Owens' article, [Google Analytics events, goals, and Meteor.js](http://joshowens.me/google-analytics-events-goals-and-meteor-js/), goes over a great way to capture how far a visitor has scrolled down a page.


### Browser Policy
If your project uses the [Browser Policy package](https://atmospherejs.com/meteor/browser-policy), we've included the Google Analytics and Mixpanel domains in our browser policy configuration. Any additional services you add will need to be added to your browser policy config as well.


#### Example browser policy
```
BrowserPolicy.content.allowOriginForAll("www.google-analytics.com");
BrowserPolicy.content.allowOriginForAll("cdn.mxpnl.com");
```

If your project doesn't use the Browser Policy package, don't worry, it won't affect your usage.


### Debugging
To log package activity to the console for debugging purposes, turn on debugging in the console:

`> analytics.debug()`

Turn debug logging off with:

`> analytics.debug(false)`


### URL Whitelisting on Android Devices

If your app is running on Android devices you will probably have to add the `cordova-plugin-whitelist` package and set access rules in your `mobile-config.js` for all URLs of the platforms that you are using.

Example for Intercom:
```
App.accessRule('https://js.intercomcdn.com/*');
App.accessRule('https://static.intercomcdn.com/*');
App.accessRule('https://api-iam.intercom.io/*');
App.accessRule('https://widget.intercom.io/*');
App.accessRule('https://nexus-websocket-a.intercom.io/*');
App.accessRule('https://nexus-websocket-b.intercom.io/*');
```

To find all the necessary URLs for your project, build your production app and install it on your Android device. Then connect it via USB and open the Android Studio Device Monitor (Tools >> Android Device Monitor >> LogCat). Perform a relevant action and then search for "whitelist". It should a show message for each URL that was blocked.


### Example React, Flow and Iron Router Apps
While page view event tracking is router agnostic, the `examples` directory contains example apps using the three most common routers used in Meteor apps: [React Router](https://github.com/ReactTraining/react-router/tree/master/packages/react-router), [Flow Router](https://github.com/kadirahq/flow-router) and [Iron Router](https://github.com/iron-meteor/iron-router). These apps can be run from within their respective directories with:

`meteor npm start`


### License
Released under the [MIT license](https://github.com/okgrow/analytics/blob/master/License.md).


### Contributing
Issues and Pull Requests are always welcome. Please read our [contribution guidelines](https://github.com/okgrow/guides/blob/master/open-source/contributing.md).
