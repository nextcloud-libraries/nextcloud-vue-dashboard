# Components for the nextcloud dashboard

> **Warning**
>
> This library is deprecated as the components are now part of [@nextcloud/vue](https://nextcloud-vue-components.netlify.app/#/Components/NcDashboard) starting with version 6.0.0.


[![npm last version](https://img.shields.io/npm/v/@nextcloud/vue-dashboard.svg?style=flat-square)](https://www.npmjs.com/package/@nextcloud/vue-dashboard) [![Dependabot status](https://img.shields.io/badge/Dependabot-enabled-brightgreen.svg?longCache=true&style=flat-square&logo=dependabot)](https://dependabot.com)

## Installation

```sh
npm install --save @nextcloud/vue-dashboard
```

## Building locally

```sh
npm link
npm ci
npm run build

# in your apps repo
npm link @nextcloud/vue-dashboard
```

## Usage

# Documentation

## DashboardWidget component

This component displays a dashboard widget with an item list as its content.

You can define an optional header, footer and empty-content.

You can set this component in loading mode.

The default item rendering can be overridden with the default slot.

Items can have a context menu.

### Item list

This component takes a list of items in the "items" prop.

If the default slot is not overridden, then do whatever you want with the item objects structure.
You will access them in your custom default item slot.

If the default slot is not overridden, DashboardWidgetItem component
is used so an items must look like:
```js static
const itemList = [
	{
		id: '1', // string or integer
		targetUrl: 'https://target.org', // the item element is a link to this URL
		avatarUrl: 'https://avatar.url/img.png', // used if avatarUsername is not defined
		avatarUsername: 'Robert', // used if avatarUrl is not defined
		avatarIsNoUser: true, // passed to the Avatar component in WidgetItem component
		overlayIconUrl: generateUrl('/svg/core/actions/sound?color=' + this.themingColor), // optional, small icon to display on the bottom-right corner of the avatar
		mainText: 'First item text',
		subText: 'First item subtext',
	},
	{
		id: '2',
		targetUrl: 'https://other-target.org',
		avatarUrl: 'https://other-avatar.url/img.png',
		overlayIconUrl: generateUrl('/svg/core/actions/add?color=' + this.themingColor),
		mainText: 'Second item text',
		subText: 'Second item subtext',
	},
]
```

### Item menu
You can optionally pass an object in the "itemMenu" prop to define a context
menu for each items. Each entry of this object must define "text" and "icon" properties.

When clicking the menu item, an event (named like the itemMenu key) will be emitted to the widget's parent.
```js static
const itemMenu = {
	// triggers an event named "markDone" when clicked
	'markDone': {
		text: t('app', 'Mark as done'),
		icon: 'icon-checkmark',
	},
	// triggers an event named "hide" when clicked
	'hide': {
		text: t('app', 'Hide'),
		icon: 'icon-toggle',
	}
}
```

### All props
* showMoreUrl: If this is set, a "show more" text is displayed on the widget's bottom. It's a link pointing to this URL.
* loading: A boolean to put the widget in a loading state
* itemMenu: An object containing context menu entries that will be displayed for each items
* items: An object containing the items themselves (specific structure must be respected except if you override item rendering with the default slot)

### Events
* for each menu item, an event named like its key is emitted with the item as a parameter

### Slots
* default (optional, default=DashboardWidgetItem): The default slot can be optionnally overridden. It contains the template of one item.
* header (optional): Something to display on top of the widget
* empty-content (optional): What to display when the item list is empty
* footer (optional): Something to display

## Simplest example with custom item
```vue
<template>
	<DashboardWidget :items="items">
		<template v-slot:default="{ item }">
			{{ item.title }}
		</template>
	</DashboardWidget>
</template>

<script>
import { DashboardWidget } from '@nextcloud/vue-dashboard'
const myItems = [
	{
		title: 'first',
		content: 'blabla',
	},
	{
		title: 'second',
		content: 'fuzzfuzz',
	},
]
export default {
	name: 'MyDashboardWidget',
	props: [],
	components: {
		DashboardWidget,
	},
	data() {
		return {
			items: myItems
		}
	},
}
</script>
```

## Complete example using DashboardWidgetItem
```vue
<template>
	<DashboardWidget :items="items"
		:show-more-url="'https://nextcloud.com'"
		:item-menu="itemMenu"
		@hide="onHide"
		@markDone="onMarkDone"
		:loading="state === 'loading'">

		<template v-slot:empty-content>
			Nothing to display
		</template>
	</DashboardWidget>
</template>

<script>
import { DashboardWidget } from '@nextcloud/vue-dashboard'
const myItems = [
	{
		id: '1',
		targetUrl: 'https://target.org',
		avatarUrl: 'https://avatar.url/img.png',
		avatarUsername: 'Robert',
		avatarIsNoUser: true,
		overlayIconUrl: generateUrl('/svg/core/actions/sound?color=' + this.themingColor),
		mainText: 'First item text',
		subText: 'First item subtext',
	},
	{
		id: '2',
		targetUrl: 'https://other-target.org',
		avatarUrl: 'https://other-avatar.url/img.png',
		overlayIconUrl: generateUrl('/svg/core/actions/add?color=' + this.themingColor),
		mainText: 'Second item text',
		subText: 'Second item subtext',
	},
]
const myItemMenu = {
	// triggers an event named "markDone" when clicked
	'markDone': {
		text: t('app', 'Mark as done'),
		icon: 'icon-checkmark',
	},
	// triggers an event named "hide" when clicked
	'hide': {
		text: t('app', 'Hide'),
		icon: 'icon-toggle',
	}
}
export default {
	name: 'MyDashboardWidget',
	props: [],
	components: {
		DashboardWidget,
	},
	data() {
		return {
			items: myItems,
			itemMenu: myItemMenu,
			loading: true,
		}
	},
	methods: {
		onMoreClick() {
			console.log('more clicked')
			const win = window.open('https://wherever.you.want', '_blank')
			win.focus()
		},
		onHide(item) {
			console.log('user wants to hide item ' + item.id)
			// do what you want
		},
		onMarkDone(item) {
			console.log('user wants to mark item ' + item.id + ' as done')
			// do what you want
		},
	},
}
</script>
```

## DashboardWidgetItem component

This component displays a dashboard widget item. It is used by default by the DashboardWidget component.
You can also use it wherever you want.

It has an optional context menu.

## Usage

### All props
* id: A unique string/integer identifying the item (optional).
* itemMenu: An object containing context menu entries that will be displayed for each items
* targetUrl: The item element is a link to this URL.
* avatarUrl: Where to get the avatar image. Used if avatarUsername is not defined.
* avatarUsername: Name to provide to the Avatar. Used if avatarUrl is not defined.
* avatarIsNoUser: Is the named a Nextcloud user name? If yes, the user's avatar image will be displayed. This value is passed to the Avatar component in WidgetItem component.
* overlayIconUrl: Small icon to display on the bottom-right corner of the avatar. Optional.
* mainText
* subText

### Context menu
You can optionally pass an object in the "itemMenu" prop to define a context
menu for each items. Each entry of this object must define "text" and "icon" properties.

When clicking the menu item, an event (named like the itemMenu key) will be emitted to the widget's parent.
```js static
const itemMenu = {
	// triggers an event named "markDone" when clicked
	'markDone': {
		text: t('app', 'Mark as done'),
		icon: 'icon-checkmark',
	},
	// triggers an event named "hide" when clicked
	'hide': {
		text: t('app', 'Hide'),
		icon: 'icon-toggle',
	}
}
```

### Events
* for each menu item, an event named like its key is emitted with the item as a parameter

## Simplest example
```vue
<template>
	<DashboardWidgetItem
		:target-url="targetUrl"
		:avatar-url="avatarUrl"
		:overlay-icon-url="overlayIconUrl"
		:main-text="mainText"
		:sub-text="subText" />
</template>

<script>
import { DashboardWidgetItem } from '@nextcloud/vue-dashboard'
const targetUrl = 'https://target.org'
const avatarUrl = 'https://avatar.url/img.png'
const overlayIconUrl = generateUrl('/svg/core/actions/sound?color=' + this.themingColor)
const mainText = 'I am an item'
const subText = 'and i can talk'
export default {
	name: 'MyRootComponentOrWhatever',
	props: [],
	components: {
		DashboardWidgetItem,
	},
	data() {
		return {
			targetUrl,
			avatarUrl,
			overlayIconUrl,
			mainText,
			subText,
		}
	},
}
</script>
```

## Complete example

```vue
<template>
	<DashboardWidgetItem
		:id="myItemId"
		:target-url="targetUrl"
		:avatar-url="avatarUrl"
		:avatar-username="avatarUsername"
		:avatar-is-no-user="avatarIsNoUser"
		:overlay-icon-url="overlayIconUrl"
		:main-text="mainText"
		:sub-text="subText"
		:item-menu="itemMenu"
		@hide="onHide"
		@markDone="onMarkDone" />
</template>

<script>
import { DashboardWidgetItem } from '@nextcloud/vue-dashboard'

const myItemId = '123abc'
const targetUrl = 'https://target.org'
const avatarUrl = 'https://avatar.url/img.png'
const avatarUsername = 'Johnny'
const avatarIsNoUser = true
const overlayIconUrl = generateUrl('/svg/core/actions/sound?color=' + this.themingColor)
const mainText = 'I am an item'
const subText = 'and i can talk'
const myItemMenu = {
	// triggers an event named "markDone" when clicked
	'markDone': {
		text: t('app', 'Mark as done'),
		icon: 'icon-checkmark',
	},
	// triggers an event named "hide" when clicked
	'hide': {
		text: t('app', 'Hide'),
		icon: 'icon-toggle',
	}
}
export default {
	name: 'MyRootComponentOrWhatever',
	props: [],
	components: {
		DashboardWidgetItem,
	},
	data() {
		return {
			myItemId,
			targetUrl,
			avatarUrl,
			overlayIconUrl,
			mainText,
			subText,
			itemMenu: myItemMenu,
		}
	},
}
</script>
```
