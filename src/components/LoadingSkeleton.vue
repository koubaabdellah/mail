<template>
	<div>
		<div v-for="i in numberOfLines" :key="i" class="item-list__entry">
			<NcAvatar v-if="withAvatar"
				class="item-avatar"
				:size="44" />
			<div class="item__details">
				<h3>&nbsp;</h3>
				<p class="message">
					&nbsp;
				</p>
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
		animation: loadingskeleton 2s ease infinite alternate,
		nudge 3s linear infinite alternate;
	}
	.item__details {
		padding-left: 8px;
		max-height: 70px;
		flex-grow: 1;
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
			animation: loadingskeleton 2s ease infinite alternate,
			nudge 3s linear infinite alternate;
			width: 60%;
		}
		.message {
			width: 100%;
			height: 15px;
			margin-top: 5px;
			animation: loadingskeleton 2s ease infinite alternate,
			nudge 3s linear infinite alternate;
		}
	}
	@keyframes loadingskeleton {
		0%   {background-color: var(--color-border);}
		25%  {background-color: var(--color-background-hover);}
		40%  {background-color: var(--color-placeholder-light);}
		50% {background-color: var(--color-loading-light);}
		65%  {background-color: var(--color-background-hover);}
		75%  {background-color: var(--color-placeholder-light);}
		80% {background-color: var(--color-loading-light);}
		90% {background-color: var(--color-placeholder-light);}
		100% {background-color: var(--color-placeholder-light);}
	}
}

</style>
