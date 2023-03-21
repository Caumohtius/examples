<template>
	<div
		class="section-additional"
		:class="{
			'mx-n45' : $device.isDesktop,
			'mx-n35' : $device.isMobileOrTablet,
			'section-additional_hov' : isHovered
		}"
	>
		<s-title
			v-if="additional.title"
			:class="{
				'mx-45 mb-35' : $device.isDesktop,
				'mx-35 mb-225' : $device.isMobileOrTablet,
			}"
			:item="additional" :link="!!$_.get(additional, 'link.url')"
		/>

		<!-- Пока не делаем
		<s-params
			v-if="$_.get(additional, 'params')" :params="additional.params && $device.isDesktop"
		/>

		<s-params
			v-if="$_.get(additional, 'params')" :params="additional.params && $device.isMobileOrTablet"
		/> -->

		<s-slider
			:items="additional.items"
			:glideOptions="{
				perView: cards,
			}"
		>
			<template #slide="{ slide }">
				<s-product-preview
					:item="slide"
					:type="type"
					@mouseover.native='isHovered = true'
					@mouseout.native='isHovered = false'
				/>
			</template>
		</s-slider>
	</div>
</template>

<script>

export default {
	data: () => ({
		isHovered: false,
	}),
	props: {
		additional: Object,
		cards: {
			type: Number,
			default: 4
		},
		type: {
			type: String,
			default: 'short'
		}
	},
}
	
</script>

<style lang="scss">

	@import '~/assets/settings';

	.section-additional {
		overflow: hidden;
		&__image {
			overflow: hidden;
		}
		&__title {
			overflow: hidden;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;
			height: 40px;
			font-size: 16px;
			line-height: 1.2;
		}
		&__price {
			margin-top: 15px;
			font-size: 14px;
			font-weight: 500;
			color: #ef781f;
			white-space: nowrap;
			&_old {
				color: rgba(0, 0, 0, .35);
				font-weight: 400;
				text-decoration: line-through;
			}
		}
		.glide {
			position:relative;
			z-index:1;
			&__arrow {
				position: absolute;
				top: calc(50% - 55px);
				width: 1em;
				height: 1em;
				font-size: 48px;
				border-radius: 50%;
				cursor: pointer;
				transition: color .2s;
				&--left {
					left: 36px;
					transform: translate(-50%, -50%);
				}
				&--right {
					right: 36px;
					transform: translate(50%, -50%);
				}
				&--disabled {
					opacity: .25;
					pointer-events: none;
				}
				&:hover {
					color: #ef781f;
				}
			}
			&__track {
				margin-right: 36px;
				margin-left: 36px;
				overflow: visible;
			}
			&__slides {
				overflow: visible;
			}				
		}
		.scroller {
			&__content {
				margin-left: -5px;
				margin-right: -5px;
				padding-bottom: .5em;
			}
			&__items {
				padding-left: 20px;
				padding-right: 20px;
			}
		}
		.layout_desktop & {
			margin-bottom: -20px;
			& .glide {
				&__track {
					padding-bottom: 20px;
				}
			}
			&_hov {
				margin-bottom: -100px;
				& .glide {
					&__track {
						padding-bottom: 100px;
					}
					&__arrow {
						top: calc(50% - 95px);
					}
				}
			}
		}
	}
</style>
