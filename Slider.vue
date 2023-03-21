<template>
	<!-- 
		Варианты использования:
		
		<s-slider
			:items="[{title: 'a'}, {title: 'b'}]"
		>
			<template #slide="{ slide, index }">
				{{ slide.title + index }}
			</template>
		</s-slider>
		
		// можно дополнить настройки для glide
		<s-slider
			:items="[{title: 'a'}, {title: 'b'}]"
			:glideOptions="{
				perView: 3
			}"
		>
			<template #slide="{ slide, index }">
				{{ slide.title + index }}
			</template>
		</s-slider>

		// вариант "принудительного" слайдера на мобильном
		<s-slider
			:items="[{title: 'a'}, {title: 'b'}]"
			:glideOptions="{
				perView: 3
			}"
			isForceGlide
		>
			<template #slide="{ slide, index }">
				{{ slide.title + index }}
			</template>
		</s-slider>

		// вариант стилей на мобильном, когда видна 1 карточка, увеличивающаяся до определённых размеров
		<s-slider
			:items="[{title: 'a'}, {title: 'b'}]"
			:glideOptions="{
				perView: 3
			}"
			adaptiveScroller
		>
			<template #slide="{ slide, index }">
				{{ slide.title + index }}
			</template>
		</s-slider>

	 -->
	<div class="slider">
		<LazyHydrate
			when-idle
			v-if="$device.isDesktop || isForceGlide"
		>
			<d-glide
				:slides="items"
				:options="glideOptionsExtended"
				setArrowLeftDisabled
				:class="`slider__cols-${glideOptionsExtended ? glideOptionsExtended.perView : 4}`"
			>
				<template #slide="{ slide, slideIndex }">
					<slot name="slide" v-bind="{ slide, index: slideIndex }" />
				</template>

				<template #arrow="{ type }">
					<template v-if="arrowsEnabled">
						<slot name="arrow" v-bind="{ type }">
							<div class="d-flex">
								<IconSliderPrev v-if="type === 'prev'" />
								<IconSliderNext v-else />
							</div>
						</slot>
					</template>
				</template>
			</d-glide>
		</LazyHydrate>
		<d-scroller
			:items="items"
			:class="{ 'scroller_adaptive' : adaptiveScroller }"
			v-else
		>
			<template #item="{ item, itemIndex }">
				<slot name="slide" v-bind="{ slide: item, index: itemIndex }" />
			</template>
		</d-scroller>
	</div>
</template>

<script>
	
export default {
	props: {
		items: Array,
		glideOptions: Object,
		isForceGlide: Boolean,
		adaptiveScroller: Boolean
	},
	computed: {
		glideOptionsExtended () {
			return Object.assign({
				animationDuration: 200,
				rewind: false,
				perView: 4,
				bound: true,
				gap: 20,				
			}, this.glideOptions);
		},
		arrowsEnabled () {
			return this.items.length > this.glideOptionsExtended.perView; 
		}
	}
}
	
</script>

<style lang="scss">
	.slider {
		.layout_desktop &{
			.glide {
				&__slide:not(:first-child) {
					margin-left: 10px;
				}
				&__slide:not(:last-child) {
					margin-right: 10px;
				}
			}
		}
		&__cols {
			&-1 {
				.glide__slide {
					width: 100%;
				}
			}
			&-2 {
				.glide__slide {
					width: calc(100% / 2 - (20px / 2));
				}
			}
			&-3 {
				.glide__slide {
					width: calc(100% / 3 - (40px / 3));
				}
			}
			&-4 {
				.glide__slide {
					width: calc(100% / 4 - (60px / 4));
				}
			}
			&-5 {
				.glide__slide {
					width: calc(100% / 5 - (80px / 5));
				}
			}
		}
		.scroller {
			&__items {
				display: inline-flex!important;
			}
		}
		.scroller_adaptive {
			.scroller {
				&__item {
					width: 90vw;
					min-width: 288px;
					max-width: 335px;
					margin-left: 5px;
					margin-right: 5px;
					flex-shrink: 0;
				}
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
		}
	}
</style>
