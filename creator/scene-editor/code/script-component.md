---
title: Using the Script Component
description: Use the Script component to give code functionality, wihtout the need to dig into the whole project structure.
---

# Script Component

With the new Script Component, we can create Entities that execute custom code from within the entity itself. 

For example, instead of going into the index.ts file, entering the main function, and adding custom behavior to an existing entity by grabbing and modifying it, we can now add a Script Component and work directly on it.

## Setting up the Script Component

1. Click on the `+` button on the Entity and add the Script Component. We can now create a new Script by clicking on **+ Add New Script Module** and choose a name, or by using the File Path (browse or drag and drop an existing file).

![](../../../.gitbook/assets/new-script-component.png)

2. We can now click on the CODE button in the component, which will open our default code editor. Now we are ready to dig into its structure. For more details on how to select and manage your default editor, please go to [Combine with code](reference-items.md).


## Understanding the Script structure

When we first open our Script, it will have the following code:

```ts
import { engine, Entity } from '@dcl/sdk/ecs'

export class BuildingScript {
  constructor(
    public src: string,
    public entity: Entity
  ) {}

  /**
   * Start function - called when the script is initialized
   */
  start() {
    // Script initialization
    console.log("BuildingScript initialized for entity:", this.entity);
  }

  /**
   * Update function - called every frame
   * @param dt - Delta time since last frame (in seconds)
   */
  update(dt: number) {
    // Called every frame
  }
}
```

The class is compes by three main parts:
* The **constructor**, 
* the **start()** method 
* the **update()** method. 

Let's explore them separately.

### Constructor

The constructor will contain the parameters you want to expose and modify dynamically from your scene in Creator Hub. 

```ts
export class BuildingScript {
  constructor(
    public src: string,
    public entity: Entity,
    public numericVariable: number, 
  ) {}
...
}
```

Save the file, click on the Refresh button in the Script Component in the Creator Hub UI, and you will now see your new parameter `numbericVariable`!

![](../../../.gitbook/assets/parameter-script-component.png)

{% hint style="warning" %}
**Important Note**: Don't modify/delete `public src: string` and `public entity: Entity`. You can add new ones following them.
{% endhint %}

The allowed types for the constructor parameters are:
* `Entity`
* `string`
* `number`
* `boolean`

{% hint style="info" %}
**📔 Note**: Both `public` and `private` constructor parameters are exposed to Creator Hub. The `private` keyword only restricts access within the `BuildingScript` class. For more details, see the official TypeScript documentation on  
[Parameter Properties](https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties).
{% endhint %}

#### Accesing Parameters inside the Script

We can access the different parameters we have defined within the class by using `this.ourParameter`. All Script components have by default on the strart() method this line: `console.log("BuildingScript initialized for entity:", this.entity);`. We can change it to `console.log("BuildingScript initialized with numericVariable:", this.numericVariable);` to log on Console the value we set on that parameter the Creator Hub.

### start() & update() Method

The **start()** method will contain code that is executed only once, when the Entity is created (in this case, when the scene first loads). 

If we preview our scene and check the logs (**Tip**: you can use the `` ` `` shortcut) we will now see our new message including the `numericVariable` parameter.

![](../../../.gitbook/assets/script-log-message.png)

The **update()** method, on the other hand, executes it's code every frame of the game (as Systems do). We might want to check the height (Position's Y axis) of the `PlayerEntity` to trigger behaviours on our script. 

The following code will print Logs every frame of the game that the `PlayerEntity` is higher than the previously defined `numericVariable`. On the Creator Hub, I can set this value dynamically, making it easy to test.

```ts
update(dt: number) {
    if (Transform.get(engine.PlayerEntity).position.y > this.numericVariable ) {
      console.log("The player's height is over ", this.numericVariable);
    }}
```

* <img src="../../../.gitbook/assets/update-script-logs.png" alt="Update Method" data-size="line">  The first log belongs to the start() method, indicating that we set numericVariable = `. The second one belongs to the update() method, when the player is higher than that value.

## See also

* [Smart items - Basics](../interactivity/smart-items.md)
* [Smart items - Advanced](../interactivity/smart-items-advanced.md)
* [States and conditions](../interactivity/states-and-conditions.md)
* [Making any item smart](../interactivity/make-any-item-smart.md)
* [SDK Quick start](../../sdk7/getting-started/sdk-101.md): follow this mini tutorial for a quick crash course.
* [Development workflow](../../sdk7/getting-started/dev-workflow.md): read this to understand scene creation from end to end.
* [Examples](https://studios.decentraland.org/resources?sdk_version=SDK7): dive right into working example scenes.
