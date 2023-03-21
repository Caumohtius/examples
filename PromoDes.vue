<template>
	<div class="section-promo-des">
		<LazyHydrate when-idle>
			<b-container class="px-0">
				<d-glide
					bullets
					:slides="slides"
					:options="{
						autoplay: autoplayDuration,
						animationDuration: 1000,
					}"
					:events="['move', 'move.after']"
					@mouseover.native="stopPreloader"
					@mouseout.native="startPreloader"
					@move="stopPreloader"
					@move-after="startPreloader"
				>
					<template #slide="{ slide }">
						<component
							:is="slide.action === 'link' && slide.link ? 'kms-link' : 'div'"
							v-bind="slide.action === 'link' && slide.link
								? {
									to: slide.link.url,
									type: slide.link.type,
									tag: 'div'
								}
								: null
							"
							class="ratio ratio-2x1"
							:class="{
								'cursor-pointer': slide.action === 'link' && slide.link || slide.action === 'form',
								'section-promo-des__slide_image': slide.type === 'image',
								'section-promo-des__slide_withText': slide.type === 'with_text',
							}"
							@click="slide.action === 'form' && $store.dispatch('modal/request/open', {
								type: 'promo'
							})"
						>
							<div
								v-if="slide.type === 'with_text'"
								class="section-promo-des__text"
							>
								<div class="section-promo-des__text-col">
									<div class="section-promo-des__tags">
										<div
											v-for="(item, i) in slide.tags"
											:key="i"
											class="section-promo-des__tag section-promo-des__tag_soon rounded"
										>
											{{ item.title }}
										</div>
									</div>
									<div class="section-promo-des__title">
										{{ slide.title }}
									</div>
									<div class="section-promo-des__description">
										{{ slide.text }}
									</div>
								</div>
							</div>

							<d-image
								v-if="slide.image && slide.type === 'with_text'"
								ratio="16x9"
								:source="{
									origin: slide.image.source,
									media: [ '885x498r', '885x498r', '885x498r' ]
								}"
								:pictureStyles="{
									backgroundPosition: 'right bottom'		
								}"
								contain
								class="section-promo-des__image_with-text mt-auto position-absolute"
							/>

							<d-image
								v-if="slide.image && slide.type === 'image'"
								class="position-absolute position-absolute_fluid"
								:source="{
									origin: slide.image.source,
									media: [ '1080x540c', '1080x540c', '1200x600c' ]
								}"
							/>
							
							<s-fluid-link
								v-if="slide.action === 'link' && slide.link && slide.link.url"
								:link="slide.link"
							/>
						</component>
					</template>
					<template #arrow="{ type }">
						<div class="d-flex" v-if="type === 'prev'">
							<IconSliderPrev />
						</div>
						<div class="d-flex" v-if="type === 'next'">
							<IconSliderNext
								v-if="type === 'next'"
							/>
							<client-only>
								<RadialProgressBar
									class="glide__progress position-absolute position-absolute_fluid d-flex"
									:style="{
										zIndex: 1
									}"
									ref="progressBar"
									v-bind="preloaderOptions"
								/>
							</client-only>
						</div>
					</template>
				</d-glide>
				<b-container>
					<div
						class="section-promo-des__badge"
						v-if="page.promo"
						v-html="page.promo"
					/>
				</b-container>			
			</b-container>
		</LazyHydrate>
	</div>
</template>

<script>

import RadialProgressBar from '~/shared/lib/radial-progress-bar/Component';
import { mapState } from "vuex";

import stateMixin from '~/shared/mixins/state';

const autoplayDuration = 5000;
	
export default {
	props: ['slides'],
	data: () => ({
		autoplayDuration,
		preloaderOptions: {
			diameter:           51,
			completedSteps:		0,
			totalSteps:			100,
			startColor:			'#EF781F',
			stopColor:			'#EF781F',
			innerStrokeColor: 	'transparent',
			strokeWidth:		3,
			innerStrokeWidth:	1.5,
			animateSpeed: 		autoplayDuration * .82,
		}
	}),
	computed: {
		...mapState('page', {
			'page': 'data'
		}),
	},	
	components: {
		RadialProgressBar,
	},
	mixins: [
		stateMixin()
	],
	methods: {
		stopPreloader () {
			this.preloaderOptions.animateSpeed = 0;
			this.preloaderOptions.completedSteps = 0;
		},
		startPreloader () {
			this.preloaderOptions.animateSpeed = autoplayDuration * .82;
			this.preloaderOptions.completedSteps = 100;
		},
	}	
}
	
</script>

<style lang="scss">

	@import '~/assets/settings';

	.section-promo-des {
		position: relative;
		background: #f0f1f5;
		margin-top: -44px;
		&__text {
			position: absolute;
			top: 50%;
			left: 40px;
			right: 40px;
			z-index: 1;
			transform: translate(0, -50%);
			&-col {
				width: 40%;
			}
		}
		&__tags {
			display: flex;
			align-items: flex-start;
			flex-direction: column;
		}
		&__tag {
			font-size: 14px;
			padding: 10px;
			& + & {
				margin-top: 8px;
			}
			&_soon {
				color: #ffffff;
				background: #000000;
			}
		}
		&__title {
			margin-top: 20px;
			font-size: 64px;
			font-weight: 500;
			line-height: 1;
			overflow: hidden;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;			
		}
		&__description {
			margin-top: 22px;
			margin-bottom: 15px;
			font-size: 21px;
			overflow: hidden;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;			
		}
		&__image_with-text {
			max-width: 885px;
			bottom: 0;
			right: 0;
			z-index: 0;
		}
		&__badge {
			top: 59px;
			position: absolute;
			padding: 12px 20px;
			background-color: #ffffff;
			border-radius: 50rem;
			border-top-left-radius: 0;
		}
		.glide {
			&__slide {
				position: relative;
				display: flex;
				flex-direction: column;
				justify-content: center;
			}
			&__arrow {
				position: absolute;
				bottom: 77px;
				width: 1em;
				height: 1em;
				font-size: 48px;
				border-radius: 50%;
				cursor: pointer;
				transition: color .2s;
				&--left {
					left: 40px;
				}
				&--right {
					left: 103px;
				}
				&--disabled {
					opacity: .25;
					pointer-events: none;
				}
				&:hover {
					color: #ef781f;
				}						
			}
			&__progress {
				top: -1.5px;
				left: -1.5px;
			}
			&__bullets {
				position: absolute;
				margin-bottom: -4px;
				margin-left: -4px;
				margin-right: -4px;
				bottom: 40px;
				left: 10px;
				display: flex;
                justify-content: center;
                width: 181px;
				flex-wrap: wrap;
			}
			&__bullet {
				margin-bottom: 4px;
				margin-left: 4px;
				margin-right: 4px;
				flex-shrink: 0;
				width: 7px;
                height: 7px;
                background: #b8b8b8;
                border-radius: 50%;
				cursor: pointer;
                &--active{
                    background: #ef781f;
                }
			}
		}		
	}		

</style>
