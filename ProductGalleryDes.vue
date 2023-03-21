<template>
	<div
		class="section-product-gallery-des"
		:class="`section-product-gallery-des_${type}`"
	>
		<div class="section-product-gallery-des__card cursor-pointer position-relative">
			<template v-for="(item, i) in gallery">
				<d-youtube
					:videoId="item.video.youtube_id"
					fullscreen
					autoplay
					:ratio="type === 'product' ? '1x1' : ''"
					:key="`video_${i}`"
					v-if="item.type === 'video' && i == currentItem"
					class="section-product-gallery-des__card-video rounded"
				>
					<template #icon-play>
						<IconPlay class="section-product-gallery-des__video-icon" />
					</template>
					<template v-if="item.video.image" #preview>
						<d-image
							v-if="type === 'product'"
							:source="{
								origin: item.video.image.source,
								media: [ '600x600c', '600x600c', '672x672c' ]
							}"
							class="position-absolute position-absolute_fluid"
						/>
						<d-image
							v-else-if="type === 'categories'"
							:source="{
								origin: item.video.image.source,
								media: $device.isMobileOrTablet
								? [ '335x178c', '335x178c', '335x178c', '335x178c' ]
								: [ '344x183c', '344x183c', '344x183c' ]
							}"
							class="position-absolute position-absolute_fluid"
						/>
					</template>
				</d-youtube>
				<d-image
					v-else-if="item.type === 'image' && i == currentItem && item.image && type === 'product'"
					:key="`image_${i}`"
					:source="{
						origin: item.image.source,
						media: [ '600x600c', '600x600c', '672x672c' ]
					}"
					:ratio="type === 'product' ? '1x1' : ''"
					class="section-product-gallery-des__card-image rounded w-100"
					@click.native="$refs.gallery.open({ index: getGalleryIndexFromIndex(i) })"
				/>
				<d-image
					v-else-if="item.type === 'image' && i == currentItem && item.image && type === 'categories'"
					:key="`image_${i}`"
					:source="{
						origin: item.image.source,
						media: $device.isMobileOrTablet
						? [ '335x178c', '335x178c', '335x178c', '335x178c' ]
						: [ '344x183c', '344x183c', '344x183c' ]
					}"
					class="section-product-gallery-des__card-image rounded w-100"
					@click.native="$refs.gallery.open({ index: getGalleryIndexFromIndex(i) })"
				/>
				<!-- предзагрузка следующего изображения -->
				<template v-if="i == currentItem && gallery[i+1] && (gallery[i+1].image || gallery[i+1].video && gallery[i+1].video.image)">
					<d-image
						v-if="type === 'product'"
						:key="`image_${i}_hidden`"
						:source="{
							origin: (gallery[i+1].image || gallery[i+1].video && gallery[i+1].video.image).source,
							media: [ '600x600c', '600x600c', '672x672c' ]
						}"
						:ratio="type === 'product' ? '1x1' : ''"
						class="section-product-gallery-des__card-image rounded w-100 section-product-gallery-des__card-image_hidden"
					/>
					<d-image
						v-else-if="type === 'categories'"
						:key="`image_${i}_hidden`"
						:source="{
							origin: (gallery[i+1].image || gallery[i+1].video && gallery[i+1].video.image).source,
							media: $device.isMobileOrTablet
							? [ '335x178c', '335x178c', '335x178c', '335x178c' ]
							: [ '344x183c', '344x183c', '344x183c' ]
						}"
						class="section-product-gallery-des__card-image rounded w-100 section-product-gallery-des__card-image_hidden"
					/>
				</template>
			</template>

			<s-card-tags
				v-if="tags"
				class="section-product-gallery-des__tags"
				:items="tags"
			/>

			<template v-for="(item, i) in gallery">
				<div
					v-if="gallery.length > 1 && i == currentItem"
					:key="`arrows_${i}`"
					class="section-product-gallery-des__arrows"
				>
					<div
						v-if="currentItem > 0"
						@click='currentItem--'
						class="section-product-gallery-des__arrow section-product-gallery-des__arrow--left d-flex"
					>
						<IconSliderPrev />
					</div>
					<div
						v-if="currentItem < (gallery.length - 1)"
						@click='currentItem++'
						class="section-product-gallery-des__arrow section-product-gallery-des__arrow--right d-flex"
					>
						<IconSliderNext />
					</div>
				</div>
			</template>
		</div>
		<b-row
			:cols="viewedPreviews"
			class="section-product-gallery-des__previews flex-nowrap"
		>
			<template v-for="(item, i) in gallery">
				<b-col
					v-if="item.type === 'video' && i < viewedPreviews && type === 'product'"
					:key="`videoP_${i}`"
					class="section-product-gallery-des__previews-item"
				>
					<d-image
						v-if="
							i === (viewedPreviews - 1) &&
							item.video.image &&
							(gallery.length - viewedPreviews > 0)
						"
						:source="{
							origin: item.video.image.source,
							media: [ '90x68c', '90x68c', '102x77c' ]
						}"
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="$refs.gallery.open()"
					>
						<span
							class="section-product-gallery-des__preview-overlay d-flex align-items-center justify-content-center"
						>
							+ {{ gallery.length - viewedPreviews }}
						</span>
					</d-image>

					<d-image
						v-else-if="item.video.image"
						ratio="4x3"
						class="section-product-gallery-des__previews-video cursor-pointer"
						:source="{
							origin: item.video.image.source,
							media: [ '90x68c', '90x68c', '102x77c' ]
						}"
						@click.native="currentItem = i"
					>
						<IconPlay v-if="currentItem != i" class="section-product-gallery-des__previews-video-icon" />
						<span
							v-if="currentItem === i"
							class="section-product-gallery-des__preview-marker"
						/>
					</d-image>
				</b-col>
				<b-col
					v-else-if="item.type === 'video' && i < viewedPreviews && type === 'categories'"
					:key="`videoP_${i}`"
					class="section-product-gallery-des__previews-item"
				>
					<d-image
						v-if="
							i === (viewedPreviews - 1) &&
							item.video.image &&
							(gallery.length - viewedPreviews > 0)
						"
						:source='{
							origin: item.video.image.source,
							media: $device.isMobileOrTablet
							? [ "48x36c", "48x36c", "48x36c", "48x36c" ]
							: [ "38x29c", "38x29c", "38x29c" ]
						}'
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="$refs.gallery.open()"
					>
						<span
							class="section-product-gallery-des__preview-overlay d-flex align-items-center justify-content-center"
						>
							+ {{ gallery.length - viewedPreviews }}
						</span>
					</d-image>

					<d-image
						v-else-if="item.video.image"
						ratio="4x3"
						class="section-product-gallery-des__previews-video cursor-pointer"
						:source='{
							origin: item.video.image.source,
							media: $device.isMobileOrTablet
							? [ "48x36c", "48x36c", "48x36c", "48x36c" ]
							: [ "38x29c", "38x29c", "38x29c" ]
						}'
						@click.native="currentItem = i"
					>
						<IconPlay v-if="currentItem != i" class="section-product-gallery-des__previews-video-icon" />
						<span
							v-if="currentItem === i"
							class="section-product-gallery-des__preview-marker"
						/>
					</d-image>
				</b-col>
		
				<b-col
					v-else-if="item.type === 'image' && i < viewedPreviews && type === 'product'"
					:key="`imageP_${i}`"
					class="section-product-gallery-des__previews-item"
				>
					<d-image
						v-if="
							i === (viewedPreviews - 1) &&
							item.image &&
							(gallery.length - viewedPreviews > 0)
						"
						:source="{
							origin: item.image.source,
							media: [ '90x68c', '90x68c', '102x77c' ]
						}"
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="$refs.gallery.open()"
					>
						<span
							class="section-product-gallery-des__preview-overlay d-flex align-items-center justify-content-center"
						>
							+ {{ gallery.length - viewedPreviews }}
						</span>
					</d-image>

					<d-image
						v-else-if="item.image"
						:source="{
							origin: item.image.source,
							media: [ '90x68c', '90x68c', '102x77c' ]
						}"
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="currentItem = i"
					>
						<span
							v-if="currentItem === i"
							class="section-product-gallery-des__preview-marker"
						/>
					</d-image>
				</b-col>
				<b-col
					v-else-if="item.type === 'image' && i < viewedPreviews && type === 'categories'"
					:key="`imageP_${i}`"
					class="section-product-gallery-des__previews-item"
				>
					<d-image
						v-if="
							i === (viewedPreviews - 1) &&
							item.image &&
							(gallery.length - viewedPreviews > 0)
						"
						:source='{
							origin: item.image.source,
							media: $device.isMobileOrTablet
							? [ "48x36c", "48x36c", "48x36c", "48x36c" ]
							: [ "38x29c", "38x29c", "38x29c" ]
						}'
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="$refs.gallery.open()"
					>
						<span
							class="section-product-gallery-des__preview-overlay d-flex align-items-center justify-content-center"
						>
							+ {{ gallery.length - viewedPreviews }}
						</span>
					</d-image>

					<d-image
						v-else-if="item.image"
						:source='{
							origin: item.image.source,
							media: $device.isMobileOrTablet
							? [ "48x36c", "48x36c", "48x36c", "48x36c" ]
							: [ "38x29c", "38x29c", "38x29c" ]
						}'
						ratio="4x3"
						class="section-product-gallery-des__previews-image cursor-pointer position-relative"
						@click.native="currentItem = i"
					>
						<span
							v-if="currentItem === i"
							class="section-product-gallery-des__preview-marker"
						/>
					</d-image>
				</b-col>
			</template>
		</b-row>
		<LazyHydrate when-idle>
			<modalGallery
				ref=gallery
				:items="gallery.map(el => el.image).filter(el => el)"
			/>
		</LazyHydrate>
	</div>
</template>

<script>
	
export default {
	props: {
		gallery: Array,
		tags: Array,
		type: {
			type: String,
			default: 'product',
		},
		viewedPreviews: {
			type: Number,
			default: 6,
		},
	},
    data: () => ({
        currentItem: 0,
    }),
	methods: {
		getGalleryIndexFromIndex (index) {
			let res = 0;
			for (let i = 0; i < index; i++) {
				if (this.gallery[i] && this.gallery[i].image) {
					res++;
				}
			}
			return res;
		}
	}
}
	
</script>

<style lang="scss">
	.section-product-gallery-des {
		&_product & {
			&__card {
				&:hover {
					.icon-play__circle {
						transition: .2s;
						fill: #ffffff;
					}
					.icon-play__triangle {
						transition: .2s;
						fill: #ef781f;
					}
					.section-product-gallery-des__arrow {
						opacity: 1;
					}
				}
				&-video {
					overflow: hidden;
				}
				&-image {
					overflow: hidden;
					&_hidden {
						visibility: hidden;
						position: absolute !important;
						z-index: -1;
					}
				}
			}
			&__tags {
				position: absolute;
				top: 10px;
				left: -10px;
				z-index: 2;
			}
			&__video-icon {
				position: absolute;
				left: 20px;
				bottom: 20px;
				z-index: 2;
				font-size: 84px;			
			}
			&__previews {
				overflow-x: auto;
				&.row {
					margin: 6px -6px;
				}
				&-item {
					&.col {
						padding: 6px;
					}
				}
				&-image, &-video {
					overflow: hidden;
					border-radius: 6px;
				}
				&-video {
					position: relative;
					&:hover {
						.icon-play__circle {
							transition: .2s;
							fill: #ffffff;
						}
						.icon-play__triangle {
							transition: .2s;
							fill: #ef781f;
						}
					}		
					&-icon {
						position: absolute;
						top: 50%;
						left: 50%;
						font-size: 30px;
						z-index: 2;
						transform: translate(-50%, -50%);
					}
				}
			}
			&__preview {
				&-overlay {
					position: absolute;
					top: 0;
					bottom: 0;
					left: 0;
					right: 0;
					z-index: 1;
					color: #ffffff;
					font-size: 16px;
					font-weight: 500;
					background-color: rgba(0, 0, 0, 0.5);
				}
				&-marker {
					position: absolute;
					display: block;
					height: 4px;
					bottom: 0;
					left: 0;
					right: 0;
					z-index: 1;
					background-color: #ef781f;
				}
			}
			&__arrow {
				position: absolute;
				top: 50%;
				width: 1em;
				height: 1em;
				font-size: 48px;
				border-radius: 50%;
				cursor: pointer;
				z-index: 2;
				transition: .2s;
				opacity: 0;
				&--left {
					left: 20px;
					transform: translate(0, -50%);
				}
				&--right {
					right: 20px;
					transform: translate(0, -50%);
				}
				&:hover {
					color: #ef781f;
				}
			}
		}
		&_categories & {
			&__card {
				&-video {
					overflow: hidden;
					&::before {
						content: '';
						display: block;
						padding-top: 53.2%;
					}
				}
				&-image {
					overflow: hidden;
					&_hidden {
						visibility: hidden;
						position: absolute !important;
						z-index: -1;
					}
					&::before {
						content: '';
						display: block;
						padding-top: 53.2%;
					}
				}
			}
			&__video-icon {
				position: absolute;
				top: 50%;
				left: 50%;
				z-index: 2;
				font-size: 84px;
				transform: translate(-50%, -50%);	
			}
			&__previews {
				&-item {
					&.col {
						padding: 4px 2.5px;
					}
				}
				&-image, &-video {
					overflow: hidden;
					border-radius: 2px;
				}
				&-video {
					position: relative;
					&:hover {
						.icon-play__circle {
							transition: .2s;
							fill: #ffffff;
						}
						.icon-play__triangle {
							transition: .2s;
							fill: #ef781f;
						}
					}		
					&-icon {
						position: absolute;
						top: 50%;
						left: 50%;
						font-size: 30px;
						z-index: 2;
						transform: translate(-50%, -50%);
					}
				}
			}
			&__preview {
				&-overlay {
					position: absolute;
					top: 0;
					bottom: 0;
					left: 0;
					right: 0;
					z-index: 1;
					color: #ffffff;
					font-size: 13px;
					font-weight: 500;
					background-color: rgba(0, 0, 0, 0.5);
				}
				&-marker {
					position: absolute;
					display: block;
					height: 2px;
					bottom: 0;
					left: 0;
					right: 0;
					z-index: 1;
					background-color: #ef781f;
				}
			}
			&__arrow {
				position: absolute;
				top: 50%;
				width: 1em;
				height: 1em;
				font-size: 48px;
				border-radius: 50%;
				cursor: pointer;
				z-index: 2;
				transition: .2s;
				&--left {
					left: 20px;
					transform: translate(0, -50%);
				}
				&--right {
					right: 20px;
					transform: translate(0, -50%);
				}
				&:hover {
					color: #ef781f;
				}
			}
			.body_desktop & {
				&__card {
					&:hover {
						.icon-play__circle {
							transition: .2s;
							fill: #ffffff;
						}
						.icon-play__triangle {
							transition: .2s;
							fill: #ef781f;
						}
						.section-product-gallery-des__arrow {
							opacity: 1;
						}
					}
				}
				&__arrow {
					opacity: 0;
				}
				&__previews {
					&.row {
						margin: 0 -2.5px;
						padding-right: 5px;
					}
					&-item {
						&.col {
							padding: 4px 2.5px;
							flex: 0 0 12.5%;
							max-width: 12.5%;
						}
					}
				}
			}
			.body_mobile & {
				&__previews {
					&.row {
						margin: 0 -3px;
						padding-right: 10px;
						padding-left: 10px;
					}
					&-item {
						&.col {
							padding: 8px 3px;
						}
					}
				}
			}
		}
	}
</style>
