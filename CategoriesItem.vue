<template>
	<b-col
		:cols="$device.isMobileOrTablet ? (!index ? 12 : 6) : (!index ? 12 : (index <= 2 ? 6 : 3))"
		:class="[`section-categories-item__col_${mod}`]"
	>
		<kms-link
			v-if="item.link && item.link.url"
			class="section-categories-item rounded"
			:class="[
				`section-categories-item_${mod}`
			]"
			:to="item.link.url"
			:type="item.link.type"
			tag=div
		>
			<div
				class="section-categories-item__text"
				:class="[
					`section-categories-item__text_${mod}`
				]"				
			>
				<div
					v-if="item.price"
					class="section-categories-item__price"
					:class="[
						`section-categories-item__price_${mod}`
					]"					
				>
					{{ item.price }}
				</div>
				<kms-link
					v-if="item.link && item.link.url"
					:to="item.link.url"
					:type="item.link.type"
					class="section-categories-item__title pe-none"
					:class="[
						`section-categories-item__title_${mod}`
					]"					
				>
					{{ item.link.title }}
				</kms-link>
			</div>
			<d-image
				v-if="item.image"
				ratio="2x1"
				:source="{
					origin: item.image.source,
					media: (
						$device.isDesktop
						? (
							mod === 'big' && [ '480x240r', '480x240r', '540x270r' ]
							|| mod === 'mid' && [ '230x115r', '230x115r', '260x130r' ]
							|| mod === 'small' && [ '235x118r', '235x118r', '265x133r' ]
						)
						: (
							mod === 'big' && [ '520x260r', '243x122r', '324x162r', '432x216r' ]
							|| mod === 'mid' && [ '255x128r', '265x133r', '233x117r', '238x119r' ]
							|| mod === 'small' && [ '255x128r', '265x133r', '233x117r', '233x116r' ]
						)
					)
				}"
				contain
				class="section-categories-item__image"
			/>
			<div
				v-else
				class="ratio ratio-2x1"
			/>
		</kms-link>
	</b-col>
</template>

<script>
	
export default {
	props: ['item', 'index'],
	computed: {
		mod (el) {
			return !el.index ? 'big' : (el.index <= 2 ? 'mid' : 'small');
		},
	},	
}
	
</script>

<style lang="scss">

	@import "~/assets/settings";

	.section-categories-item {
		position: relative;
		background-color: #f9f9f9;
		font-weight: 500;
		font-size: 16px;
		cursor: pointer;
		overflow: hidden;
		&__price {
			font-size: 14px;
			color: #ef781f;
		}

		&__text {
			position: relative;
			display: flex;
			flex-direction: column;
			justify-content: center;
		}


		.layout_desktop & {
			margin: 10px 0;
			&_big, &_mid {
				display: flex;
				align-items: center;		
			}
			&_big {
				padding: 20px;
			}
			&_mid {
				padding: 20px 20px 20px 10px;
			}
			&_small {
				padding-top: 112.5px;
				padding-bottom: 20px;
				& .section-categories-item__text {
					position: absolute;
					top: 20px;
					display: block;
				}
			}
			&__title {
				font-size: 16px;
				&_big {
					font-size: 36px;
				}
				&_mid {
					font-size: 27px;
				}
			}
			&__image {
				z-index: 1;
			}
			&__text {
				z-index: 2;
				padding-right: 20px;
				padding-left: 20px;
				&_big, &_mid {
					min-width: 50%;
				}
			}
			&::before {
				content: '';
				position: absolute;
				top: 0;
				right: 0;
				width: 285px;
				height: 285px;
				background-color: #ef781f;
				border-radius: 50%;
				transform: translate(100%, -100%);
				transition: .5s;
			}
			&:hover::before {
				transform: translate(50%, -50%);
			}
		}
		.layout_mobile & {
			margin: 5px 0;
			&__title {
				font-size: 16px;
				@media (max-width: 374px) {
					font-size: 14px;
				}			
				&_big {
					font-size: 18px;
					@include media-breakpoint-up(sm) {
						font-size: 21px;
					}				
					@include media-breakpoint-up(md) {
						font-size: 27px;
					}				
					@media (min-width: 992px) {
						font-size: 36px;
					}				
				}
				&_mid {
					@media (min-width: 992px) {
						font-size: 27px;
					}
				}
			}
			&__text {
				&_big {
					padding-right: 20px;
					padding-left: 20px;
					@include media-breakpoint-only(xs) {
						margin-top: 10px;
					}
					@include media-breakpoint-up(sm) {
						min-width: 55%;
					}
				}			
				&_mid, &_small {
					padding-right: 10px;
					padding-left: 10px;
				}
				&_mid {
					@media (min-width: 992px) {
						padding-right: 20px;
						padding-left: 20px;
						min-width: 50%;
					}
				}
			}
			&_small {
				padding-top: 87.75px;
				padding-bottom: 10px;
				& .section-categories-item__text {
					position: absolute;
					top: 10px;
					display: block;
				}
			}
			&_mid {
				@media (max-width: 991px) {
					padding-top: 87.75px;
					padding-bottom: 10px;
					& .section-categories-item__text {
						position: absolute;
						top: 10px;
						display: block;
					}
				}
				@media (min-width: 992px) {
					padding-top: 20px;
					padding-bottom: 20px;
					display: flex;
					align-items: center;
				}
			}
			&_big {
				@include media-breakpoint-up(sm) {
					padding-top: 20px;
					padding-bottom: 20px;
					display: flex;
					align-items: center;
				}	
			}
			&__col {
				&_big {
					max-width: 100%;
				}
				&_small, &_mid {
					@include media-breakpoint-up(sm) {
						max-width: calc(100% / 2);
					}
					@include media-breakpoint-up(md) {
						max-width: calc(100% / 3);
					}
				}
				&_small {
					@media (min-width: 992px) {
						max-width: calc(100% / 4);
					}
				}
				&_mid {
					@media (min-width: 992px) {
						max-width: calc(100% / 2);
					}
				}
			}
		}
	}
</style>
