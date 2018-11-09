# Adaptive Card Designer (Beta)

The Adaptive Card Designer provides a rich, interactive design-time experience for authoring [adaptive cards](https://adaptivecards.io). 

Try it out at [https://adaptivecards.io/designer](https://adaptivecards.io/designer)

![Designer Screenshot](https://adaptivecards.io/images/designer.png)

## What is this package?

This package allows you to easily integrate the adaptive cards designer into your own experiences. 

## Beta notice 

**NOTE**: The designer is currently considered beta and **may have breaking changes** in the public API as we get feedback.

## Usage

There are two simple ways to consume the designer: CDN script reference or importing the module and using webpack.

### CDN <script> references

The simplest way to get started it to include 3 <script> tags in your page. 

* **monaco-editor** - provides a rich JSON-editing experience
* **adaptivecards-designer** - the designer component
* **markdown-it** - [optional] automatic markdown support for the designer and cards

```html
<!-- markdown-it isn't required but enables out-of-the-box markdown support -->
<script src="https://unpkg.com/markdown-it@8.4.0/dist/markdown-it.min.js"></script>

<!-- NOTE: monaco-editor is required for the designer to work. We currently provide it in the CDN, but this may change later -->
<script src="https://unpkg.com/adaptivecards-designer@0.1.1/dist/monaco-editor/min/vs/loader.js"></script>

<script src="https://unpkg.com/adaptivecards-designer@0.1.1/dist/adaptivecards-designer.min.js"></script>

<script type="text/javascript">
	window.onload = () => {

		let hostContainers = [];

		// Optional: add the default Microsoft Host Apps (see docs below)
		// hostContainers.push(new ACDesigner.WebChatContainer("Bot Framework WebChat", "containers/webchat-container.css"));
		 
		let designer = new ACDesigner.CardDesigner(hostContainers);

		// The designer requires various CSS and image assets to work properly, 
		// If you've loaded the script from a CDN it needs to know where these assets are located
		designer.assetPath = "https://unpkg.com/adaptivecards-designer@0.1.1/dist";

		// Initialize monaco-editor for the JSON-editor pane. The simplest way to do this is use the code below, since we currently bundle monaco into our CDN distribution. 
		require.config({ paths: { 'vs': 'https://unpkg.com/adaptivecards-designer@0.1.0/dist/monaco-editor/min/vs' } });
		require(['vs/editor/editor.main'], function () {
			designer.monacoEditorLoaded();
		});

		designer.attachTo(document.getElementById("designerRootHost");
	};
</script>

<body>
   <div id="designerRootHost"></div>
</body>
```

### Node + webpack

If you already use webpack and want to to bundle the designer, you need a few packages. **adaptivecards-designer**, **monaco-editor** for the JSON editor, and **markdown-it** for markdown handling. You can use another markdown processor if you choose.

```console
npm install adaptivecards-designer monaco-editor markdown-it --save
```

You also need some development dependencies to bundle monaco, and copy some CSS+image files into your output.

```console
npm install copy-webpack-plugin monaco-editor-webpack-plugin css-loader style-loader --save-dev
```

Then in your app, use the following imports and API. The code below was authored in TypeScript, but can be easily modified to plain JS. 

```js
import * as monaco from "monaco-editor/esm/vs/editor/editor.api";
import * as markdownit from "markdown-it";
import * as Designer from "adaptivecards-designer";
import "./app.css";
import "adaptivecards/dist/adaptivecards-default.css";
import "adaptivecards-controls/dist/adaptivecards-controls.css";
import "adaptivecards-designer/dist/adaptivecards-designer.css";

window.onload = () => {
	if (!Designer.SettingsManager.isLocalStorageAvailable) {
		console.log("Local storage is not available.");
	}

	let hostContainers: Array<Designer.HostContainer> = [];
	hostContainers.push(new Designer.WebChatContainer("Bot Framework WebChat", "containers/webchat-container.css"));

	let designer = new Designer.CardDesigner(hostContainers);
	Designer.CardDesigner.processMarkdown = (text) => { return markdownit(text) };
	designer.monacoModuleLoaded(monaco);
	designer.attachTo(document.getElementById("designerRootHost"));
};

```


## Advanced configuration

```js

	/* Add the default Microsoft Host Apps 	*/ 
 
	hostContainers.push(new ACDesigner.WebChatContainer("Bot Framework WebChat", "containers/webchat-container.css"));
	hostContainers.push(new ACDesigner.CortanaContainer("Cortana Skills", "containers/cortana-container.css"));
	hostContainers.push(new ACDesigner.OutlookContainer("Outlook Actionable Messages", "containers/outlook-container.css"));
	hostContainers.push(new ACDesigner.TimelineContainer("Windows Timeline", "containers/timeline-container.css"));
	hostContainers.push(new ACDesigner.DarkTeamsContainer("Microsoft Teams - Dark", "containers/teams-container-dark.css"));
	hostContainers.push(new ACDesigner.LightTeamsContainer("Microsoft Teams - Light", "containers/teams-container-light.css"));
	hostContainers.push(new ACDesigner.BotFrameworkContainer("Bot Framework Other Channels (Image render)", "containers/bf-image-container.css"));
	hostContainers.push(new ACDesigner.ToastContainer("Windows Notifications (Preview)", "containers/toast-container.css"));


	/* Modify the toolbar */
	designer.toolbar.addElement(new ToolbarSeparator());
	designer.toolbar.addElement(
		new ToolbarButton(
			"Save",
			null,
			(sender) => {
				// Here is how to get the payload of the current card from the designer
				let card = designer.getCard();

				alert(JSON.stringify(card, null, 4));
			}
		)
	)
	

	/* Collapse certain panes by default (BEFORE designer attached)	*/
	designer.treeViewPane.collapse();
	designer.jsonEditorPane.collapse();
	

	/* Set the card payload in the designer: (AFTER designer attached) */ 
	designer.setCard(
		{
			type: "AdaptiveCard",
			version: "1.0",
			body: [
				{
					type: "TextBlock",
					text: "Hello world"
				}
			]
		}
	);
};
```

## Full sample code

See the [full example here](https://unpkg.com/adaptivecards-designer@0.1.0/dist/index-cdn.html)

## Learn more at http://adaptivecards.io
* [Documentation](http://adaptivecards.io/documentation/)
* [Schema Explorer](http://adaptivecards.io/explorer/)
* [Sample Cards](http://adaptivecards.io/samples/)