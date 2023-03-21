<template>
	<footer class="footer-mob mt-45" v-if="footer">
		<b-container>
			<b-row class="footer-mob__row pt-4">
				<b-col cols="6">
					<kms-link to="/">
						<img
							src="~/assets/images/logo.svg"
							:alt="footer.logo_alt"
							class="footer-mob__logo"
						/>
					</kms-link>

					<div
						class="footer-mob__text mt-3"
						v-if="page.promo"
						v-html="page.promo"
					/>

					<div class="footer-mob__phones">
						<div 
							v-for="(phone, i) in footer.phones"
							:key="i"
							class="footer-mob__phone"
						>
							<a
								:href="`tel:${phone}`"
								class="rs-phone"
							>
								{{ phone | phone('8 (000) 000-00-00') }}
							</a>
						</div>
					</div>

					<div class="footer-mob__info">
						<div 
							v-for="(item, i) in footer.requisites"
							class="footer-mob__info-item my-3"
							:key="i"
						>
							{{ item }}
						</div>
					</div>					
				</b-col>

				<b-col cols="6">

					<el-payments class="footer-mob__payments" />

					<div class="footer-mob__socials mt-4">
						<div class="my-n1 mx-n1">
							<div
								v-for="(url, key) in footer.social"
								:key="key"
								class="footer-mob__socials-item my-1 mx-1 d-inline-block"
							>
								<a
									:href="url"
									target="_blank"
									rel="noopener noreferrer nofollow"
									class="footer-mob__socials-link circle-icon d-flex align-items-center justify-content-center"
								>
									<IconVkontakte
										v-if="key === 'vk'"
									/>
									<IconYoutube
										v-else-if="key === 'yt'"
									/>
									<IconTikTok
										v-else-if="key === 'tk'"
									/>
								</a>
							</div>
						</div>
					</div>
				</b-col>
			</b-row>

		</b-container>
		
	</footer>
</template>

<script>

import { mapState } from "vuex";

export default {
	computed: {
		...mapState('footer', {
			'footer': 'data'
		}),
		...mapState('page', {
			'page': 'data'
		}),
	},	
}
	
</script>

<style lang="scss">
	.footer-mob {
		&__row {
			border-top: solid 1px #f0f0f0;
		}
		&__logo {
			width: 80px;
			height: 56px;
		}
		&__text {
			font-size: 14px;
		}
		&__phones {
			margin-top: 44px;
			line-height: 30px;
		}
		&__payments {
			margin: 72px -2.5px -2.5px -2.5px;
		}		
		&__socials {
			&-link {
				transition: color .2s, background-color .2s;
				&:hover {
					color: #ffffff;
					background-color: #ef781f;
					box-shadow: none;
				}
			}
		}
		&__info {
			margin-top: 38px;
			padding-bottom: 60px;
			&-item {
				font-size: 14px;
				line-height: 1;
				color: rgba(0, 0, 0, .35);
			}
		}
	}
</style>
