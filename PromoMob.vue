<template>
	<div class="section-promo-mob">
		<LazyHydrate when-idle>
			<b-container class="px-0">
				<d-glide
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
					bullets
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
							class="section-promo-mob__slide ratio w-100"
							:class="{
								'section-promo-mob__slide_image': slide.type === 'image',
								'section-promo-mob__slide_withText': slide.type === 'with_text',
							}"
							@click="slide.action === 'form' && $store.dispatch('modal/request/open', {
								type: 'promo'
							})"
						>
							<div 
								v-if="slide.type === 'with_text'"
								class="section-promo-mob__text"
							>
								<div class="section-promo-mob__tags">
									<div
										v-for="(item, i) in slide.tags"
										:key="i"
										class="section-promo-mob__tag section-promo-mob__tag_soon rounded"
									>
										{{ item.title }}
									</div>
								</div>
								<div class="section-promo-mob__title">
									{{ slide.title }}
								</div>
								<div class="section-promo-mob__description">
									{{ slide.text }}
								</div>
							</div>

							<d-image
								v-if="slide.image && slide.type === 'with_text'"
								ratio="16x9"
								:source="{
									origin: slide.image.source,
									media: [ '560x315r', '540x304r', '720x405r', '960x540r' ]
								}"
								:pictureStyles="{
									backgroundPosition: 'right bottom'		
								}"
								contain
								class="section-promo-mob__image_with-text position-absolute"
							/>

							<d-image
								v-if="slide.image && slide.type === 'image'"
								class="position-absolute position-absolute_fluid"
								:source="{
									origin: slide.image.source,
									media: [ '560x836c', '540x806c', '720x1075c', '960x1434c' ]
								}"
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
						class="section-promo-mob__badge"
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

	.section-promo-mob {
		background: #f0f1f5;
		position: relative;
		@include media-breakpoint-only(xs) {
			margin-left: -20px;
			margin-right: -20px;
		}
		&__slide {
			position: relative;
			&::before {
				padding-top: 149.33%;
			}
		}
		&__text {
			position: absolute;
			left: 20px;
			right: 20px;
			top: 0;
			z-index: 1;
		}
		&__tags {
			margin-top: 70px;
			display: flex;
			align-items: flex-start;
			flex-direction: column;
		}
		&__tag {
			font-size: 12px;
			padding: 8px;
			& + & {
				margin-top: 8px;
			}
			&_soon {
				color: #ffffff;
				background: #000000;
			}
		}
		&__title {
			margin-top: 15px;
			font-size: 44px;
			font-weight: 500;
			line-height: 1;
			overflow: hidden;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;			
		}
		&__description {
			margin-top: 15px;
			margin-bottom: 15px;
			overflow: hidden;
			display: -webkit-box;
			-webkit-line-clamp: 2;
			-webkit-box-orient: vertical;			
		}
		&__image {
			&_with-text {
				bottom: 0;
				right: 0;
				left: 0;
				z-index: 0;
			}
		}
		&__badge {
			top: 10px;
			position: absolute;
			padding: 12px 20px;
			background-color: #ffffff;
			font-size: 14px;
			border-radius: 50rem;
			border-top-left-radius: 0;
			white-space: nowrap;
			@media (max-width: 374px) {
				padding: 12px 13px;
				font-size: 13px;
			}
		}
		& .container {
			padding-right: 20px;
			padding-left: 20px;
		}
		.glide {
			&__arrow {
				position: absolute;
				bottom: 57px;
				width: 1em;
				height: 1em;
				font-size: 48px;
				border-radius: 50%;
				cursor: pointer;
				&--left {
					left: 20px;
				}
				&--right {
					left: 83px;
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
				bottom: 20px;
				left: 10px;
				display: flex;
                justify-content: center;
                width: 141px;
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
                &--active{
                    background: #ef781f;
                }
			}			
		}		
	}

</style>
