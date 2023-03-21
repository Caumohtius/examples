<template>
	<header class="header-mob" ref="headerMob" v-if="header">

		<s-old-website-link-mob />

		<b-container>
			<div class="header-mob__top">
				<div class="d-flex align-items-center py-3">
					<div class="header-mob__city-wrapper mr-auto pr-25">
						<div class="header-mob__city-caption">
							Ваш город:
						</div>

						<div
							v-if="header.location"
							v-b-modal.modal-city-mob
							class="header-mob__city position-relative alt-link"
							:class="{'header-mob__city_read-only' : header.location.read_only }"
						>
							{{ $_.get(header, 'location.city') }}
						</div>
					</div>

					<div class="header-mob__phone">
						<a 
							v-if="header.phone"
							:href="`tel:${header.phone}`"
							class="link rs-phone"
						>
							{{ header.phone | phone('8 (000) 000-00-00') }}
						</a>
					</div>
					
					<a 
						v-for="(item, i) in header.messengers"
						:key="i"
						:href="item.url"
						target="_blank"
						rel="noopener noreferrer nofollow"				
						class="header-mob__contact ml-3"
						:class="{
							'header-mob__contact_viber' : item.title === 'Viber',
							'header-mob__contact_wa' : item.title === 'WhatsApp',
							'header-mob__contact_tg' : item.title === 'Telegram'
						}"
					>
						<IconViberFill v-if="item.title === 'Viber'" />
						<IconWhatsApp v-if="item.title === 'WhatsApp'" />
						<IconTelegram v-if="item.title === 'Telegram'" />
					</a>
				</div>

				<div
					v-if="header.xmas_decor"
					class="header-mob__decor-garland"
				/>
			</div>

			<div class="header-mob__bottom d-flex align-items-center py-25 justify-content-center">

				<div class="mr-auto">
					<div
						class="p-2 ml-n2"
						v-b-modal.modal-menu
					>
						<IconBurger />
					</div>
				</div>



				<div
					class="mr-auto"
					:class="[header.xmas_decor, 'd-flex']"
				>
					<div
						v-if="header.xmas_decor"
						class="flex-shrink-0 flex-grow-1 text-right"
					>
						<div class="header-mob__decor-tree mr-3" />
					</div>

					<kms-link class="header-mob__link d-flex align-items-center flex-grow-0" to="/">
						<img 
							src="~/assets/images/logo.svg"
							:alt="header.logo_alt"
							class="header-mob__logo"
						/>
						
						<div
							v-if="page.promo"
							v-html="page.promo"
							class="header-mob__logo-desc"
						/>
					</kms-link>
				</div>

			</div>
		</b-container>
	</header>
</template>

<script>

import { mapState } from "vuex";

export default {
	computed: {
		...mapState('header', {
			'header': 'data'
		}),
		...mapState('page', {
			'page': 'data'
		}),
	},
	methods: {
		onScroll () {
			document.documentElement.style.setProperty('--headerHeight', Math.max((this.$el.offsetHeight || 0) - window.pageYOffset, 0) + 'px');
		}
	},
	mounted () {
		window.addEventListener("scroll", this.onScroll, {passive: true});
		this.onScroll();
	},
	destroyed () {
		window.removeEventListener('scroll', this.onScroll, {passive: true});
	},
}
	
</script>

<style lang="scss">
	.header-mob {
		font-size: 12px;
		&__top {
			position: relative;
			height: 3.4rem;
			border-bottom: 1px solid #f0f0f0;
		}
		&__bottom {
			height: 3.7rem;
			font-size: 16px;
			box-shadow: inset 0 -1px #f0f0f0;
		}
		&__logo {
			width: 50px;
			height: 35px;
			&-desc {
				max-width: 140px;
				margin-left: 10px;
				font-size: 11px;
				line-height: 12px;
				flex-shrink: 1;
			}
		}
		&__link {

			&:hover {
				color: #000000;
			}
		}
		&__city {
			max-width: min-content;
			text-overflow: ellipsis;
			white-space: nowrap;
			overflow: hidden;
			transition: .2s;
			font-weight: 500;
			font-size: 12px;
			line-height: 16px;
			box-shadow: 0 1px #ef781f;
			&:hover {
				box-shadow: 0 1px #000000;
			}
			&_read-only {
				color: #000000;
				pointer-events: none;
			}
			&-caption {
				font-size: 10px;
				line-height: 1;
				color: #7f7f7f;
			}
			&-wrapper {
				max-width: 50%;
				min-width: 75px;
			}
		}
		&__phone {
			text-align: right;
			white-space: nowrap;
			font-weight: 500;
			font-size: 12px;
		}
		&__decor-tree {
			margin-left: 60px;
			width: 23px;
			height: 35px;
			background: center / 23px 35px no-repeat url(~assets/images/xmastree.png);
			@media (-webkit-min-device-pixel-ratio: 2), 
			(min-resolution: 192dpi) {
				background: center / 23px 35px no-repeat url(~assets/images/2xmastree.png);
			}
		}
		&__decor-garland {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 19px;
			background: center top / 375px 19px no-repeat url(~assets/images/garland-mob.png);
			@media (-webkit-min-device-pixel-ratio: 2), 
			(min-resolution: 192dpi) {
				background: center top / 375px 19px no-repeat url(~assets/images/2garland-mob.png);
			}
		}
		&__contact {
			font-size: 18px;
			&_viber, &_viber:hover {
				color: #7360f2;
			}
			&_wa, &_wa:hover {
				color: #48c95f;
			}
			&_tg, &_tg:hover {
				color: #2aa0da;
			}
		}
	}
</style>
