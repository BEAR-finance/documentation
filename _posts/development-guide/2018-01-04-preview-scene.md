---
date: 2018-01-01
title: Preview your scene
description: What you can see in a scene's preview
redirect_from:
  - /documentation/preview-scene/
  - /getting-started/preview-scene/
categories:
  - development-guide
type: Document
set: getting-started
---

Once you have [built a new scene](https://docs.decentraland.org/#create-your-first-scene) or downloaded a [scene example](https://github.com/decentraland-scenes/Awesome-Repository#examples) you can preview it locally.

## Before you begin

Please make sure you first install the CLI tools by running the following command:

```bash
npm install -g decentraland
```

See the [Installation Guide]({{ site.baseurl }}{% post_url /development-guide/2018-01-01-installation-guide %}) for more details instructions.

## Preview a scene

To preview a scene run the following command on the scene's main folder:

```bash
dcl start
```

Any dependencies that are missing are installed and then the CLI opens the scene in a new browser tab automatically. It creates a local web server in your system and points the web browser tab to this local address.

Every time you make changes to the scene, the preview reloads and updates automatically, so there's no need to run the command again.

> Note: Some scenes depend on an external server to store a shared state for all players in the scene. When previewing one of these scenes, you'll likely have to also run the server locally on another port. Check the scene's readme for instructions on how to launch the server as well as the scene.

## Upload a scene to decentraland

Once you're happy with your scene, you can upload it and publish it to Decentraland, see [publishing]({{ site.baseurl }}{% post_url /development-guide/2018-01-07-publishing %}) ) for instructions on how to do that.

## Parameters of the preview command

You can add the following flags to the `dcl start` command to change its behavior:

- `--no-browser` to prevent the preview from opening a new browser tab.
- `--port` to assign a specific port to run the scene. Otherwise it will use whatever port is available.
- `--no-debug` Disable the debug panel, that shows scene and performance stats
- `--w` or `--no-watch` to not open watch for filesystem changes and avoid hot-reload
- `--c` or `--ci` To run the parcel previewer on a remote unix server

> Note: To preview old scenes that were built for older versions of the SDK, you must set the corresponding version of `decentraland-ecs` in your project's `package.json` file.

## Preview scene size

The scene size shown in the preview is based on the scene's configuration, you set this when building the scene using the CLI. By default, the scene occupies a single parcel (16 x 16 meters).

If you're building a scene to be uploaded to several adjacent parcels, you can edit the _scene.json_ file to reflect this, listing multiple parcels in the "parcels" field. Placing any entities outside the bounds of the listed parcels will display them in red.

```json
 "scene": {
    "parcels": [
      "0,0",
      "0,1",
      "1,0",
      "1,1"
    ],
    "base": "0,0"
  },
```

> Tip: While running the preview, the parcel coordinates don't need to match those that your scene will really use, as long as they're adjacent and are arranged into the same shape. You will have to replace these with the actual coordinates later when you [deploy the scene](#upload-a-scene-to-decentraland).

## Debug a scene

Running a preview provides some useful debugging information and tools to help you understand how the scene is rendered. The preview mode provides indicators that show parcel boundaries and the orientation of the scene.

If the scene can't be compiled, you'll just see the grid on the ground, with nothing rendered on it.

If this occurs, there are several places where you can look for error messages to help you understand what went wrong:

1.  Check your code editor to make sure that it didn't mark any syntax or logic errors.
2.  Check the output of the command line where you ran `dcl start`
3.  Check the JavaScript console in the browser for any other error messages. For example, when using Chrome you access this through `View > Developer > JavaScript console`.
4.  If you're running a preview of a multiplayer scene that runs together with a local server, check the output of the command line window where you run the local server.

If an entity is located or extends beyond the limits of the scene, it will be displayed in red to indicate this, with a red bounding box to mark its boundaries. Nothing in your scene can extend beyond the scene limits. This won't stop the scene from being rendered locally, but it will stop the offending entities form being rendered in Decentraland.

#### Use the console

Output messages to console (using `log()`). You can then view these messages as they are generated by opening the JavaScript console of your browser. For example, when using Chrome you access this through `View > Developer > JavaScript console`.

You can also add `debugger` commands or use the `sources` tab in the developer tools menu to add breakpoints and pause execution while you interact with the scene in real time.

Once you deploy the scene, you won't be able to see the messages printed to console when you visit the scene in-world. If you need to check these messages on the deployed scene, you can turn the scene's console messages back on adding the following parameter to the URL: `DEBUG_SCENE_LOG`.

#### View scene stats

The lower-left corner of the preview informs you of the _FPS_ (Frames Per Second) with which your scene is running. Your scene should be able to run above 25 FPS most of the time.

Click the _P_ key to open the Panel. This panel displays the following information about the scene, and is updated in real time as things change:

- Processed Messages
- Pending on Queue
- Scene location (preview vs deployed)
- Poly Count
- Textures count
- Materials count
- Entities count
- Meshes count
- Bodies count
- Components count

The processed messages and message queue refer to the messages sent by your scene's code to the engine. These are useful to know if your scene is running more operations than the engine can support. If many messages get queued up, that's usually a bad sign.

The other numbers in the panel refer to the usage of resources, in relation to the [scene limitations]({{ site.baseurl }}{% post_url /development-guide/2018-01-06-scene-limitations %}). Keep in mind that the maximum allowed number for these values is proportional to the amount of parcels in the scene. If your scene tries to render an entity that exceeds these values, for example if it has too many triangles, it won't be rendered in-world once deployed.

> Note: Keeping this panel open can negatively impact the frame rate and performance of your scene, so we recommend closing it while not in use.

#### Run code only in preview

You can detect if a scene is running as a preview or is already deployed in production, so that the same code behaves differently depending on the case. You can use this to add debugging logic to your code without the risk of forgetting to remove it and having it show in production.

To use this function, import the `@decentraland/EnvironmentAPI` library.

```ts
import { isPreviewMode } from '@decentraland/EnvironmentAPI'

executeTask(async () => {
  const preview: boolean = await isPreviewMode()

  if (preview){
    log("Running in preview")
  }
}
```

> Note: `isPreviewMode()` needs to be run as an [async function]({{ site.baseurl }}{% post_url /development-guide/2018-02-25-async-functions %}), since the response may delay in returning data.

<!--
## View collision meshes

While viewing the preview, you can press `c` to view any collision meshes loaded in the glTF models of the scene. These are usually invisible, but determine where an avatar can move through, and where it can't.

![]({{ site.baseurl }}/images/media/collision-meshes.png)

Collision meshes can be added to any model in an external 3D modeling tool like Blender. Large models like houses often include these, they are usually a lot simpler geometrically than the original shape, as this implies much less computational requirements. Stairs typically use a simplified collision mesh like a ramp to make it easier to climb. See [colliders]({{ site.baseurl }}{% post_url /3d-modeling/2018-01-12-colliders %}) for more details.

## View bounding boxes

While viewing the preview, you can press `b` to view any bounding boxes. Bounding boxes show the space occupied by an entity, it's especially useful to see these when dealing with entities that are invisible, buried inside others, or underground.
-->

## Connecting to Ethereum network

If your scene makes use of transactions over the Ethereum network, for example if it prompts you to pay a sum in MANA to open a door, you must manually add an additional parameter to the preview URL: `&ENABLE_WEB3`

So for example if your scene opens in the following URL:
`http://127.0.0.1:8000/?SCENE_DEBUG_PANEL&position=0%2C0`

Add the parameter at the end, like this:
`http://127.0.0.1:8000/?SCENE_DEBUG_PANEL&position=0%2C0&ENABLE_WEB3`

#### Using the Ethereum test network

You can avoid using real currency while previewing the scene. For this, you must use the _Ethereum Ropsten test network_ and transfer fake MANA instead. To use the test network you must set your Metamask Chrome extension to use the _Ropsten test network_ instead of _Main network_. You must also own MANA in the Ropsten blockchain, which you can acquire for free from Decentraland.

<!--
To preview your scene using the test network, add the `DEBUG` property to the URL you're using to access the scene preview on your browser. For example, if you're accessing the scene via `http://127.0.0.1:8000/?position=0%2C-1`, you should set the URL to `http://127.0.0.1:8000/?DEBUG&position=0%2C-1`.
-->

Any transactions that you accept while viewing the scene in this mode will only occur in the test network and not affect the MANA balance in your real wallet.
