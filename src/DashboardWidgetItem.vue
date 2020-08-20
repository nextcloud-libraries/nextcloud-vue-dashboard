<!--
 - @copyright Copyright (c) 2020 Julien Veyssier <eneiluj@posteo.net>
 -
 - @author Julien Veyssier <eneiluj@posteo.net>
 -
 - @license GNU AGPL version 3 or any later version
 -
 - This program is free software: you can redistribute it and/or modify
 - it under the terms of the GNU Affero General Public License as
 - published by the Free Software Foundation, either version 3 of the
 - License, or (at your option) any later version.
 -
 - This program is distributed in the hope that it will be useful,
 - but WITHOUT ANY WARRANTY; without even the implied warranty of
 - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 - GNU Affero General Public License for more details.
 -
 - You should have received a copy of the GNU Affero General Public License
 - along with this program. If not, see <http://www.gnu.org/licenses/>.
 -
 -->
<docs>
This component displays a dashboard widget item. It is used by default by the DashboardWidget component.
You can also use it wherever you want.

It displays the item given as a prop with optional:
* context menu

## Usage

### Item
The item object passed as a prop must respect this structure:
```js static
const item = {
	targetUrl: 'https://target.org', // the item element is a link to this URL
	avatarUrl: 'https://avatar.url/img.png', // used if avatarUsername is not defined
	avatarUsername: 'Robert', // used if avatarUrl is not defined
	overlayIconUrl: generateUrl('/svg/core/actions/sound?color=' + this.themingColor), // optional, small icon to display on the bottom-right corner of the avatar
	mainText: 'First item text',
	subText: 'First item subtext',
}
```

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

### All props
* itemMenu: An object containing context menu entries that will be displayed for each items
* item: An object containing the item itself (specific structure must be respected)

### Events
* for each menu item, an event named like its key is emitted with the item as a parameter

## Simplest example
```vue
<template>
	<DashboardWidgetItem :item="item" />
</template>

<script>
import DashboardWidgetItem from '../components/DashboardWidgetItem'
const myItem = {
	targetUrl: 'https://target.org',
	avatarUrl: 'https://avatar.url/img.png',
	overlayIconUrl: generateUrl('/svg/core/actions/sound?color=' + this.themingColor),
	mainText: 'I am an item',
	subText: 'and i can talk',
}

export default {
	name: 'MyRootComponentOrWhatever',
	props: [],
	components: {
		DashboardWidgetItem,
	},
	data() {
		return {
			item: myItem
		}
	},
}
</script>
```

## Complete example

```vue
<template>
	<DashboardWidgetItem :item="item"
		:itemMenu="itemMenu"
		@hide="onHide"
		@markDone="onMarkDone"
		/>
</template>

<script>
import DashboardWidgetItem from '../components/DashboardWidgetItem'
const myItem = {
	targetUrl: 'https://target.org',
	avatarUrl: 'https://avatar.url/img.png',
	overlayIconUrl: generateUrl('/svg/core/actions/sound?color=' + this.themingColor),
	mainText: 'I am an item',
	subText: 'and I can talk',
}

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
			item: myItem,
			itemMenu: myItemMenu
		}
	},
}
</script>
```

</docs>

<template>
	<div @mouseover="hovered = true" @mouseleave="hovered = false">
		<!--div class="popover-container">
			<Popover :open="popoverEnabled && hovered" placement="top" class="content-popover" offset="40">
				<template>
					<slot name="popover" :item="item">
						{{ t('core', 'Undefined popover content') }}
					</slot>
				</template>
			</Popover>
		</div-->
		<a :href="item.targetUrl" target="_blank" :class="{'item-list__entry': true, 'item-list__entry--has-actions-menu': gotMenu}">
			<slot name="avatar" :item="item">
				<Avatar
					class="item-avatar"
					:size="44"
					:url="item.avatarUrl"
					:user="item.avatarUsername" />
			</slot>
			<img v-if="item.overlayIconUrl"
				class="item-icon"
				alt=""
				:src="item.overlayIconUrl">
			<div class="item__details">
				<h3 :title="item.mainText">
					{{ item.mainText }}
				</h3>
				<p class="message" :title="item.subText">
					{{ item.subText }}
				</p>
			</div>
			<Actions v-if="gotMenu" :force-menu="true" menu-align="right">
				<ActionButton v-for="(m, id) in itemMenu"
					:key="id"
					:icon="m.icon"
					:close-after-click="true"
					@click.prevent.stop="$emit(id, item)">
					{{ m.text }}
				</ActionButton>
			</Actions>
		</a>
	</div>
</template>

<script>
import Avatar from '@nextcloud/vue/dist/Components/Avatar'
import Actions from '@nextcloud/vue/dist/Components/Actions'
import ActionButton from '@nextcloud/vue/dist/Components/ActionButton'
export default {
	name: 'DashboardWidgetItem',
	components: {
		// Popover
		Avatar, Actions, ActionButton,
	},

	props: {
		item: {
			type: Object,
			required: true,
		},
		itemMenu: {
			type: Object,
			default: () => { return {} },
		},
		/* popoverEnabled: {
			type: Boolean,
			default: false,
		}, */
	},

	data() {
		return {
			hovered: false,
		}
	},

	computed: {
		gotMenu() {
			return Object.keys(this.itemMenu).length !== 0
		},
	},

	watch: {
	},

	mounted() {
	},

	methods: {

	},
}
</script>

<style scoped lang="scss">
.item-list__entry {
	display: flex;
	align-items: flex-start;
	padding-right: 8px;
	padding-left: 8px;
	padding-top: 8px;
	padding-bottom: 8px;

	// Account for action menu
	&--has-actions-menu {
		padding-right: 0px !important;
	}

	&:hover,
	&:focus {
		background-color: var(--color-background-hover);
		border-radius: var(--border-radius-large);
	}
	.item-avatar {
		position: relative;
		margin-top: auto;
		margin-bottom: auto;
	}
	.item__details {
		padding-left: 8px;
		max-height: 44px;
		flex-grow: 1;
		overflow: hidden;
		display: flex;
		flex-direction: column;

		h3,
		.message {
			white-space: nowrap;
			overflow: hidden;
			text-overflow: ellipsis;
		}
		.message span {
			width: 10px;
			display: inline-block;
			margin-bottom: -3px;
		}
		h3 {
			font-size: 100%;
			margin: 0;
		}
		.message {
			width: 100%;
			color: var(--color-text-maxcontrast);
		}
	}

	.item-icon {
		position: relative;
		width: 14px;
		height: 14px;
		margin: 27px -3px 0px -7px;
	}

	button.primary {
		padding: 21px;
		margin: 0;
	}
}
/*
.content-popover {
	height: 0px;
	width: 0px;
	margin-left: auto;
	margin-right: auto;
}
.popover-container {
	width: 100%;
	height: 0px;
}
*/
</style>
