<template>
	<footer class="footer-des py-45" v-if="footer">
		<div class="footer-des__wrapper rounded mx-auto">
			<b-container class="py-45">
				<b-row cols="10">
					<b-col cols="2">
						<kms-link to="/">
							<img
								src="~/assets/images/logo.svg"
								:alt="footer.logo_alt"
								class="footer-des__logo"
							/>
						</kms-link>

						<div
							class="footer-des__text mt-3"
							v-if="page.promo"
							v-html="page.promo"
						/>

						<div class="footer-des__text mt-3">
							Мы принимаем к оплате
						</div>

						<el-payments class="footer-des__payments" />

					</b-col>
					<b-col
						cols="2"
						v-for="(group,i) in footer.menu"
						:key="i"
					>
						<div class="footer-des__title">
							{{group.caption}}
						</div>
						<div
							v-for="(item, i) in group.items"
							:key="i"
							class="my-2"
						>
							<kms-link
								:to="$_.get(item, 'url')"
								:type="item.type"
							>
								{{ item.title }}
							</kms-link>
						</div>
					</b-col>
					<b-col offset="1" cols="3" class="footer-des__contacts">
						<div class="footer-des__phones">
							<div class="footer-des__title">
								Позвоните нам
							</div>
							<div
								v-for="(phone, i) in footer.phones"
								:key="i"
								class="footer-des__phone my-2"
							>
								<a
									:href="`tel:${phone}`"
									class="d-inline-block rs-phone"
								>
									{{ phone | phone('8 (000) 000-00-00') }}
								</a>
							</div>
						</div>
				
						<div class="footer-des__socials">
							<div class="footer-des__title">
								Подпишитесь на нас
							</div>
							<div class="footer-des__socials-items d-flex">
								<div
									v-for="(url, key) in footer.social"
									:key="key"
									class="footer-des__socials-item my-1"
								>
									<a
										:href="url"
										target="_blank"
										rel="noopener noreferrer nofollow"
										class="footer-des__socials-link circle-icon d-flex align-items-center justify-content-center"
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
				<b-row cols="10">
					<b-col
						v-for="(item, i) in footer.requisites"
						class="footer-des__info-item"
						cols="2"
						:key="i"
					>
						{{ item }}
					</b-col>
				</b-row>
			</b-container>
		</div>
		
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

	@import '~/assets/settings';

	.footer-des {
		margin-top: 24px;
		&__wrapper {
			width: max-content;
			background: #ffffff;
			box-shadow: 4px 4px 40px rgba(0, 0, 0, .05);
			line-height: 1.3;
			border: 1px solid rgba(0, 0, 0, .1);
		}
		&__logo {
			width: 80px;
			height: 56px;
		}
		&__text {
			font-size: 14px;
		}
		&__title {
			font-weight: 500;
			font-size: 21px;
		}
		&__payments {
			margin-top: 10px;
			padding: -2.5px;
		}
		&__socials {
			&-items {
				margin: 15px -5px;
			}
			&-item {
				padding: 0 5px;
			}
			&-link {
				transition: color .2s, background-color .2s;
				&:hover {
					color: #FFFFFF;
					background-color: #EF781F;
					box-shadow: none;
				}
			}
		}
		&__info-item {
			margin-top: 60px;
			font-size: 14px;
			color: rgba(0, 0, 0, .35);
			white-space: nowrap;
		}
	}
</style>
