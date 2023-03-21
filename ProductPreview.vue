<template>
	<kms-link
		tag=div
		v-if="item.link && item.link.url"
		:to="item.link.url"
		:type="item.link.type"
		class="section-product-preview"
		:class="[
			`section-product-preview_${type}`,
			navLastItemActive && 'section-product-preview_active'
		]"
		@mousedown.native="navLastItemTrigger($event)"
		@touchstart.native="navLastItemTrigger($event)"
		:data-test-article="item.article"
	>
		<div class="section-product-preview__image-wrapper ratio ratio-1x1">

			<d-img
				class="section-product-preview__image position-absolute position-absolute_fluid rounded"
				v-if="item.image"
				:source="{
					origin: item.image.source,
					media: (
						$device.isDesktop
						? (
							type === 'short' && [ '235x235c', '235x235c', '265x265c' ]
							|| type === 'full' && [ '235x235c', '235x235c', '275x275c' ]
							|| type === 'test' && [ '230x230c', '230x230c', '230x230c' ]
						)
						: (
							type === 'short' && [ '160x160c', '160x160c', '160x160c', '160x160c' ]
							|| type === 'full' && [ '160x160c', '160x160c', '160x160c', '160x160c' ]
							|| type === 'test' && [ '160x160c', '160x160c', '160x160c', '160x160c' ]
						)
					)
				}"		
			/>

			<s-card-tags
				v-if="item.tags && type !== 'short'"
				:items="item.tags"
				class="section-product-preview__tags"
			/>

			<s-vendor-code
				v-if="item.article"
				:item="item.article"
				class="section-product-preview__vendore-code"
			/>

			<div
				class="section-product-preview__colors-column d-flex flex-column"
				v-if="item.colors && item.colors.length && $device.isMobileOrTablet && type !== 'short'"
			>
				<s-color
					v-for="(color, i) in item.colors"
					:key="i"
					:item="color"
					class="section-product-preview__color"
				/>
			</div>

			<div
				v-if="item.characteristics && item.characteristics.length && type !== 'short'"
				class="section-product-preview__characteristics d-flex rounded overflow-hidden"
			>
				<div 
					v-for="(item, i) in item.characteristics"
					:key="i"
					class="section-product-preview__characteristics-item"
				>
					<div
						v-if="item.label"
						v-html="item.label"
						class="section-product-preview__characteristics-label"
					/>

					<div
						v-html="typeof item === 'string' ? item : item.value"
						class="section-product-preview__characteristics-value"
					/>
				</div>
			</div>

		</div>

		<div class="section-product-preview__title position-relative">
			<div
				v-if="type === 'full'"
				class="section-product-preview__title-top"
			>
				<span
					class="section-product-preview__category d-inline-block"
					:class="{
						'd-inline-block text-truncate' : $device.isDesktop
					}"
				>
					{{ item.category }}
				</span>
				<span
					class="section-product-preview__producer"
				>
					{{ item.producer }}
				</span>
			</div>

			<kms-link
				v-if="item.link && item.link.url"
				:to="item.link.url"
				:type="item.link.type"
				class="section-product-preview__title-bottom pe-none"
			>
				{{ item.link.title }}
			</kms-link>
		</div>
		
		<div
			class="section-product-preview__colors-row d-flex mt-225"
			v-if="item.colors && item.colors.length && $device.isDesktop && type !== 'short'"
		>
			<s-color
				v-for="(color, i) in item.colors"
				:key="i"
				:item="color"
				class="section-product-preview__color my-1"
			/>
		</div>

		<div class="section-product-preview__bottom d-flex justify-content-between align-items-center position-relative">
			<el-cost
				v-if="item.cost"
				:cost="item.cost"
				class="section-product-preview__price"
			/>

			<span
				v-if="item.availability.status !== 'in_stock'"
				class="section-product-preview__not-avail"
			>
				нет в наличии
			</span>

			<s-product-btn-indicators
				v-if="$device.isDesktop"
				:product="item"
				class="section-product-preview__indicators"
			/>
			
		</div>

		<div
			class="section-product-preview__interact"
			:id="`catalog-item-product-${item.id}-card-interact`"
		>
			<s-product-btn-fast
				v-if="$device.isDesktop && type !== 'short' && item.availability.status === 'in_stock'"
				class="w-100 mt-3"
				goalClick="kupit_v_1_klik_miniatyura"
				goalModalSubmit="otpravit_miniatyura"
				:product="item"
				data-test-id="product-preview__btn_fast"
			/>
			<s-product-btn-request
				v-if="item.availability.status !== 'in_stock' && type !== 'short'"
				class="w-100 mt-3"
				:product="item"
				:status="item.availability.status"
				idPrefix="preview"
			/>

			<div class="section-product-preview__buttons position-relative">
				
				<template v-if="item.availability.status === 'in_stock' || type !== 'short'">
					<s-product-btn-compare
						:product="item"
						:tooltip="$device.isDesktop"
						:tooltipContainer="`catalog-item-product-${item.id}-card-interact`"
						idPrefix="catalog"
						:disabled="item.availability.status !== 'in_stock'"
						data-test-id="product-preview__btn_compare"
					/>
					<s-product-btn-favorite
						:product="item"
						:tooltip="$device.isDesktop"
						:tooltipContainer="`catalog-item-product-${item.id}-card-interact`"
						idPrefix="catalog"
						:disabled="item.availability.status !== 'in_stock'"
						data-test-id="product-preview__btn_favorite"
					/>
					<s-product-btn-cart
						:product="item"
						:tooltip="$device.isDesktop"
						:tooltipContainer="`catalog-item-product-${item.id}-card-interact`"
						idPrefix="catalog"
						primary
						:disabled="item.availability.status !== 'in_stock'"
						goalClickMetrika="v_korzinu_miniatyura"
						data-test-id="product-preview__btn_cart"
					/>
				</template>

				<s-product-btn-request
					v-if="item.availability.status !== 'in_stock' && type === 'short'"
					class="w-100"
					:product="item"
					:status="item.availability.status"
					idPrefix="preview"
				/>
			</div>
		</div>

		<s-fluid-link
			v-if="item.link && item.link.url"
			:link="item.link"
			class="section-product-preview__fluid-link"
		/>

	</kms-link>
</template>

<script>

import navLastItemMixin from '~/shared/mixins/navLastItem';
import onVisibleInViewportMixin from '~/mixins/onVisibleInViewport';

export default {
	props: {
		item: Object,
		type: {
			type: String,
			default: 'full'
		},
		contextType: String
	},
	mixins: [
		navLastItemMixin(),
		onVisibleInViewportMixin,
	],
	methods: {
		isNavLastItemEnabled () {
			return this.contextType === 'category'
		},
		onVisibleInViewport () {
			try {
				const GEData = this.$_.get(this, 'item.meta.google_ecommerce');
				if (GEData && window.gtag) {
					window.gtag(`event`, `view_item_list`, {
						items: [ GEData ]
					});
				}
			} catch (error) {
				console.error(error);
			}
		}
	}
}
	
</script>

<style lang="scss">
	.section-product-preview {
		position: relative;
		&__image-wrapper {
			position: relative;
		}
		&_active &__image-wrapper {
			box-shadow: 0 0 0 3px white, 0 0 0 5px #e27d39;
			border-radius: 5px;
		}
		&__characteristics {
			position: absolute;
			box-shadow:inset 0 0 0 1px #f0f0f0, 4px 4px 40px rgba(0, 0, 0, 0.05);
			background-color: #ffffff;
			&-item {
				flex: 33.3333% 1 1;
				text-align: center;
				line-height: 1;
				white-space: nowrap;
			}
			&-label {
				font-size: 10px;
				line-height: 1;
				color: rgba(0, 0, 0, .3)
			}
		}
		&__title {
			line-height: 1.2;
		}
		&__category {
			color: rgba(0, 0, 0, .5);
		}
		&__producer {
			font-weight: 500;
		}
		&__buttons {
			display: flex;
		}
		&__tags {
			position: absolute;
			top: 7px;
			left: -7px;
		}
		&__vendore-code {
			position: absolute;
		}
		&__not-avail {
			font-size: 14px;
			line-height: 1.2;
			color: rgba(0, 0, 0, .35);
		}
		.body_desktop & {
			cursor: pointer;
			&__vendore-code {
				opacity: 0;
				transition: .2s;
				top: 5px;
				right: 5px;
			}
			&__characteristics {
				padding: 4px 10px;
				bottom: 0;
				left: 10px;
				right: 10px;
				transform: translate(0, 50%);
				&-item {
					padding-right: 4px;
					padding-left: 4px;
					font-size: 14px;
					display: flex;
					justify-content: center;
					flex-direction: column;
				}
				&-label {
					margin-bottom: 2px;
				}
			}
			&__title {
				font-size: 16px;
				&-bottom {
					overflow: hidden;
					display: -webkit-box;
					-webkit-line-clamp: 3;
					-webkit-box-orient: vertical;
					text-overflow: ellipsis;
				}
				&-top {
					display: flex;
					justify-content: space-between;
				}
			}
			&__colors-row {
				flex-wrap: wrap;
				margin-left: -4px;
				margin-right: -4px;
				overflow: hidden;
				position: relative;
			}
			&__color {
				margin: 0 4px;
				font-size: 10px;
				flex-shrink: 0;
			}
			&::before {
				content: '';
				display: none;
				position: absolute;
				background-color: #ffffff;
				margin: -15px -15px 0 -15px;
				top: 0;
				left: 0;
				right: 0;
				bottom: 0;
				border-radius: 6px;
				box-shadow: inset 0 0 0 1px #f0f0f0, 4px 4px 40px rgba(0, 0, 0, .15);
			}
			&:hover::before {
				display: block;
				z-index: 3;
			}
			&:hover .section-product-preview__interact {
				display: block;
				visibility: visible;
			}
			&:hover .section-product-preview__indicators {
				display: none;
				visibility: hidden;
			}
			&:hover .section-product-preview__image-wrapper,
			&:hover .section-product-preview__title,
			&:hover .section-product-preview__colors-row,
			&:hover .section-product-preview__bottom {
				z-index: 3;
			}
			&:hover .section-product-preview__interact {
				z-index: 4;
			}
			&:hover .section-product-preview__vendore-code {
				opacity: 1;
			}
			&:not(:hover) .section-card-tags__hide-text {
				transform: translate(-100%, 0);
			}
			&__buttons {
				justify-content: center;
				margin-top: 15px;
			}
			&__interact {
				position: absolute;
				bottom: 16.2px;
				left: 0;
				right: 0;
				display: none;
				visibility: hidden;
			}
			&__producer {
				white-space: nowrap;
			}
			&__price {
				margin-top: 15px;
				margin-bottom: 16.2px;
			}
			&__fluid-link {
				z-index: 3;
			}
		}
		.body_mobile & {
			height: 100%;
			width: 160px;
			&__characteristics {
				padding: 4px 6px;
				bottom: 5px;
				left: -2px;
				right: -2px;
				transform: translate(0, 100%);
				&-item {
					padding-right: 2px;
					padding-left: 2px;
					font-size: 11px;
				}
			}
			&__title {
				font-size: 14px;
			}
			&__colors-column {
				position: absolute;
				flex-wrap: wrap;
				bottom: 32px;
				right: 6px;
				margin-top: -2.5px;
				margin-bottom: -2.5px;
				height: 75%;
				overflow: hidden;
			}
			&__color {
				margin: 2.5px 1px;
				font-size: 8px;
				flex-shrink: 0;
			}
			&__bottom {
				margin-top: 10px;
			}
			&__buttons {
				display: flex;
				margin-top: 10px;
				@media (max-width: 374px) {
					margin-top: 10px;
				}
				.product-btn {
					& + .product-btn {
						margin-left: 8px;
					}			
				}
			}
			.s-color_gloss .s-color__inner:after {
				top: 1px;
			}
			&__fluid-link {
				z-index: 1;
			}
			&__interact {
				position: relative;
				z-index: 2;
			}
			.product-btn-request {
				padding: 10px 15px !important;
				&_discontinued {
					padding: 10px 45px 10px 18px !important;
				}
			}
			&__vendore-code {
				padding: 5px 7px;
				bottom: 7px;
				right: 5px;
			}
		}
		&_full & {
			&__title {
				margin-top: 30px;
				&-bottom {
					margin-top: 8px;
				}
			}
		}
		.body_desktop &_full {
			padding-bottom: 126px;
			margin-bottom: -126px;
		}
		.body_desktop &_full &	{
			&__fluid-link {
				height: calc(100% - 127px)!important;
			}
		}
		.body_mobile &_full {
			@media (max-width: 374px) { 
				width: 135px;
			}
		}
		.body_mobile &_full & {
			&__buttons {
				@media (max-width: 374px) {
					margin-top: 10px;
				}
				.product-btn {
					@media (max-width: 374px) {
						width: 36px;
						height: 36px;
						font-size: 12px;
					}		
				}
			}
			&__title {
				&-bottom, &-top {
					overflow: hidden;
					display: -webkit-box;
					-webkit-line-clamp: 2;
					-webkit-box-orient: vertical;
					text-overflow: ellipsis;
				}
			}
		}
		.body_desktop &_short {
			padding-bottom: 63px;
			margin-bottom: -63px;
		}
		.body_desktop &_short & {
			&__title {
				margin-top: 20px;
			}
			&__fluid-link {
				height: calc(100% - 63px)!important;
			}
		}
		.body_mobile &_short {
			margin-right: 5px;
			margin-left: 5px;
		}
		.body_mobile &_short & {
			&__title {
				margin-top: 13px;
				&-bottom {
					overflow: hidden;
					display: -webkit-box;
					-webkit-line-clamp: 3;
					-webkit-box-orient: vertical;
					text-overflow: ellipsis;
				}
			}
		}
		.body_desktop &_test {
			padding-bottom: 125px;
			margin-bottom: -125px;
		}
		.body_desktop &_test & {
			&__title {
				margin-top: 20px;
			}
			&__fluid-link {
				height: calc(100% - 127px)!important;
			}
		}
		.body_mobile &_test {
			margin-right: 5px;
			margin-left: 5px;
		}
		.body_mobile &_test & {
			&__title {
				margin-top: 35px;
				&-bottom {
					overflow: hidden;
					display: -webkit-box;
					-webkit-line-clamp: 3;
					-webkit-box-orient: vertical;
					text-overflow: ellipsis;
				}
			}
		}
	}
</style>