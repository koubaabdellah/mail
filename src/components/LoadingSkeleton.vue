<template>
	<div>
		<div v-for="i in numberOfLines" :key="i" class="item-list__entry">
			<NcAvatar v-if="withAvatar" class="item-avatar" :size="44" />
			<div class="item__details">
				<h3>&nbsp;</h3>
				<p class="message">
					&nbsp;
				</p>
			</div>
		</div>
	</div>
</template>

<script>
import NcAvatar from '@nextcloud/vue/dist/Components/NcAvatar'

export default {
	name: 'LoadingSkeleton',
	components: {
		NcAvatar,
	},
	props: {
		numberOfLines: {
			type: Number,
			default: 10,
		},
		withAvatar: {
			type: Boolean,
			default: true,
		},
	},
}
</script>

<style lang="scss" scoped>

/* skeleton */
.item-list__entry {
	display: flex;
	align-items: flex-start;
	padding: 8px;
	.item-avatar {
		position: relative;
		margin-top: auto;
		margin-bottom: auto;
		background-color: var(--color-background-hover) !important;
		animation-name: loadingskeleton;
		animation-duration: 2s;
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
			background-color: var(--color-background-hover);
		}
		h3 {
			font-size: 100%;
			margin: 0;
			animation-name: loadingskeleton;
			animation-duration: 2s;
		}
		.message {
			width: 80%;
			height: 15px;
			margin-top: 5px;
			animation-name: loadingskeleton;
			animation-duration: 2s;
		}
	}
	@keyframes loadingskeleton {
		0%   {background-color: var(--color-placeholder-dark);}
		25%  {background-color: var(--color-background-hover);}
		40%  {background-color: var(--color-background-darker);}
		60%  {background-color: var(--color-background-hover);}
		80% {background-color: var(--color-background-darker);}
		100% {background-color: var(--color-background-dark);}
	}
}

</style>
