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

It has an optional context menu.

## Usage

### All props
* itemMenu: An object containing context menu entries that will be displayed for each items
* targetUrl: The item element is a link to this URL.
* avatarUrl: Where to get the avatar image. Used if avatarUsername is not defined.
* avatarUsername: Name to provide to the Avatar. Used if avatarUrl is not defined.
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
		:targetUrl="targetUrl"
		:avatarUrl="avatarUrl"
		:overlayIconUrl="overlayIconUrl"
		:mainText="mainText"
		:subText="subText" />
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
		:targetUrl="targetUrl"
		:avatarUrl="avatarUrl"
		:overlayIconUrl="overlayIconUrl"
		:mainText="mainText"
		:subText="subText"
		:itemMenu="itemMenu"
		@hide="onHide"
		@markDone="onMarkDone" />
</template>

<script>
import { DashboardWidgetItem } from '@nextcloud/vue-dashboard'

const targetUrl = 'https://target.org'
const avatarUrl = 'https://avatar.url/img.png'
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

</docs>

<template>
	<div @mouseover="hovered = true" @mouseleave="hovered = false">
		<component :is="targetUrl ? 'a' : 'div'"
			:href="targetUrl"
			:target="targetUrl ? '_blank' : undefined"
			:class="{ 'item-list__entry': true, 'item-list__entry--has-actions-menu': gotMenu }"
			@click="onLinkClick">
			<slot name="avatar" :avatarUrl="avatarUrl" :avatarUsername="avatarUsername">
				<Avatar
					class="item-avatar"
					:size="44"
					:url="avatarUrl"
					:user="avatarUsername"
					:show-user-status="!gotOverlayIcon" />
			</slot>
			<img v-if="overlayIconUrl"
				class="item-icon"
				alt=""
				:src="overlayIconUrl">
			<div class="item__details">
				<h3 :title="mainText">
					{{ mainText }}
				</h3>
				<p class="message" :title="subText">
					{{ subText }}
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
		</component>
	</div>
</template>

<script>
import Avatar from '@nextcloud/vue/dist/Components/Avatar'
import Actions from '@nextcloud/vue/dist/Components/Actions'
import ActionButton from '@nextcloud/vue/dist/Components/ActionButton'
export default {
	name: 'DashboardWidgetItem',
	components: {
		Avatar, Actions, ActionButton,
	},

	props: {
		/**
		 * The item element is a link to this URL (optional)
		 */
		targetUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Where to get the avatar image. (optional) Used if avatarUsername is not defined.
		 */
		avatarUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Name to provide to the Avatar. (optional) Used if avatarUrl is not defined.
		 */
		avatarUsername: {
			type: String,
			default: undefined,
		},
		/**
		 * Small icon to display on the bottom-right corner of the avatar (optional)
		 */
		overlayIconUrl: {
			type: String,
			default: undefined,
		},
		/**
		 * Item main text (mandatory)
		 */
		mainText: {
			type: String,
			required: true,
		},
		/**
		 * Item subline text (optional)
		 */
		subText: {
			type: String,
			default: '',
		},
		/**
		 * An object containing context menu entries that will be displayed for each items (optional)
		 */
		itemMenu: {
			type: Object,
			default: () => { return {} },
		},
	},

	data() {
		return {
			hovered: false,
		}
	},

	computed: {
		item() {
			return {
				targetUrl: this.targetUrl,
				avatarUrl: this.avatarUrl,
				avatarUsername: this.avatarUsername,
				overlayIconUrl: this.overlayIconUrl,
				mainText: this.mainText,
				subText: this.subText,
			}
		},
		gotMenu() {
			return Object.keys(this.itemMenu).length !== 0
		},
		gotOverlayIcon() {
			return this.overlayIconUrl && this.overlayIconUrl !== ''
		},
	},

	watch: {
	},

	mounted() {
	},

	methods: {
		onLinkClick(event) {
			if (event.target.tagName === 'BUTTON') {
				event.preventDefault()
			}
		},
	},
}
</script>

<style scoped lang="scss">
.item-list__entry {
	display: flex;
	align-items: flex-start;
	position: relative;
	padding: 8px;

	// Account for action menu
	&--has-actions-menu {
		padding-right: 0 !important;
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
