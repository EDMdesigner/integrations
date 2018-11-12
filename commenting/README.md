# Commenting Plugin Integrations

1) [generate an access token](../generate-access-token)
2) Insert the following snippet into your HTML (you can change the version number to the latest one)

```html
<script src="https://plugins.edmdesigner.com/commenting/0.0.2/createPlugin.js">
<script src="https://plugins.edmdesigner.com/commenting/0.0.2/openCommentingPlugin.js">
<style>
#commenting-plugin-wrapper {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100vh;
	margin-top: 100vh;
	transition: all 0.3s ease;
	background-color: #efefef;
}

#commenting-plugin-wrapper iframe {
	width: 100%;
	height: 100%;
	border: 0;
}
</style>
```

3) Initialize the commenting plugin from javascript. You can finx an example for the documentJSON [here](http://plugins.edmdesigner.com/commenting/0.0.2/static/greenest.js); Important! Every element in the document has to have an element id. (eId). It means that you have to use the newest editor version.
```javascript
openCommentingPlugin({
	wrapper: document.getElementById("commenting-plugin-wrapper"), //this element should be placed to the end of the DOM.
	documentJSON: "YOUR DOCUMENT JSON",
	userName: "Test Name",
	userId: "86113",
	userAvatar: "https://www.arcamax.com/jsnews/images/comics/dilbert.png",
	JWT: "YOUR ACCESS TOKEN"
});
```



You can find an example integration [here](http://plugins.edmdesigner.com/commenting/0.0.2/example.html).
