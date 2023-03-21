<template>
	<div class="section-product-gallery-mob">
		<div class="section-product-gallery-mob__gallery mx-n35">
			<div class="section-product-gallery-mob__row position-relative d-flex flex-nowrap">
				<template v-for="(item, i) of gallery">
					<div
						v-if="item.type === 'video'"
						class="section-product-gallery-mob__col position-relative"
						:key="`video_${i}`"
					>
						<d-youtube
							:videoId="item.video.youtube_id"
							fullscreen
							autoplay
							ratio="1x1"
							class="section-product-gallery-mob__video rounded"
						>
							<template #icon-play>
								<IconPlay class="section-product-gallery-mob__video-icon" />
							</template>
							<template v-if="item.video.image" #preview>
								<d-image
									:source="{
										origin: item.video.image.source,
										media: [ '300x300c', '300x300c', '300x300c', '300x300c' ]
									}"
									class="position-absolute position-absolute_fluid"
								/>
							</template>
						</d-youtube>
						<s-card-tags
							v-if="i == 0"
							class="section-product-gallery-mob__tags" :items="tags"
						/>
					</div>
					<div
						v-else-if="item.type === 'image'"
						:key="`image_${i}`"
						class="section-product-gallery-mob__col"
					>
						<d-image
							v-if="item.image"
							:source="{
								origin: item.image.source,
								media: [ '300x300c', '300x300c', '300x300c', '300x300c' ]
							}"
							ratio="1x1"
							class="section-product-gallery-mob__image rounded"
							@click.native="$refs.gallery.open({ index: getGalleryIndexFromIndex(i) })"
						/>
					</div>
				</template>
			</div>
		</div>
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
	props: ['gallery', 'tags'],
    data: () => ({
        currentItem: 0,
		viewedPreviews: 6,
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
	.section-product-gallery-mob {
		&__row {
			padding-left: 15px;
			padding-right: 15px;
			padding-bottom: 10px;
			overflow-x: auto;
		}
		&__col {
			padding-left: 5px;
			padding-right: 5px;
		}
		&__video, &__image {
			overflow: hidden;
			width: 80vw;
			max-width: 300px;
			min-width: 256px;
		}	
		&__video-icon {
			position: absolute;
			left: 10px;
			bottom: 10px;
			z-index: 2;
			font-size: 75px;			
		}
		&__tags {
			position: absolute;
			top: 15px;
			left: 1px;
			z-index: 2;
		}
	}
</style>
