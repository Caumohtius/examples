<template>
	<header class="header-des" v-if="header">

		<b-container>
			<div class="header-des__top">
				<div class="d-flex align-items-center py-35">
					<div class="header-des__city-wrapper mr-425">
						<span class="header-des__city-caption d-inline-block">
							Ваш город:
						</span>

						<div
							v-if="header.location"
							v-b-modal.modal-city-des
							:title="$_.get(header, 'location.city')"
							class="header-des__city header-des__accent d-inline-block alt-link"
							:class="{'header-des__city_read-only' : header.location.read_only }"
						>
							{{ header.location.city }}
						</div>
					</div>

					<div 
						v-if="header.location && header.location.delivery"
						:title="header.location.delivery"
						v-html="header.location.delivery"
						class="header-des__address mr-425"
					/>

					<div 
						v-else-if="header.location && header.location.address"
						:title="header.location.address"
						class="header-des__address-wrapper mr-425"
					>
						<span class="header-des__city-caption mr-0">
							Адрес выставочного зала:
						</span>

						<span
							v-html="header.location.address"
							class="header-des__address"
						/>

						<kms-link
							v-if="header.location.link && header.location.link.url"
							:to="header.location.link.url"
							:type="header.location.link.type"
							class="header-des__accent alt-link ml-1"
						>
							{{ header.location.link.title }}
						</kms-link>
					</div>

					<div
						class="mr-auto"
						v-html="$_.get(header, 'location.schedule')"
					/>

					<a 
						v-for="(item, i) in header.messengers"
						:key="i"
						:href="item.url"
						target="_blank"
						rel="noopener noreferrer nofollow"				
						class="header-des__contact ml-3"
						:class="{
							'header-des__contact_viber' : item.title === 'Viber',
							'header-des__contact_wa' : item.title === 'WhatsApp',
							'header-des__contact_tg' : item.title === 'Telegram'
						}"
					>
						<IconViberFill v-if="item.title === 'Viber'" />
						<IconWhatsApp v-if="item.title === 'WhatsApp'" />
						<IconTelegram v-if="item.title === 'Telegram'" />
					</a>

					<div
						id="header-des__phone"
						class="header-des__phone ml-35"
						tabindex="0"
					>
						<a 
							v-if="header.phone"
							:href="`tel:${header.phone}`"
							class="rs-phone"
						>
							{{ header.phone | phone('8 (000) 000-00-00') }}
						</a>
						<b-tooltip
							target="header-des__phone"
							triggers="hover blur"
							placement="bottom"
							variant="white"
							boundary-padding="0"
						>
							<div
								@click="$store.dispatch('modal/request/open', {
									type: 'callback'
								})"
								class="px-225 py-1 alt-link"
							>
								Заказать звонок
							</div>
						</b-tooltip>
					</div>
				</div>

				<div
					v-if="header.xmas_decor"
					class="header-des__decor-tree"
				/>

				<div
					v-if="header.xmas_decor"
					class="header-des__decor-garland"
				/>

			</div>
		</b-container>

		<modal-city-des />

	</header>
</template>

<script>

import { mapState } from "vuex";

export default {
	computed: {
		...mapState('header', {
			'header': 'data'
		}),
	},
}
	
</script>

<style lang="scss">
	
	@import '~/assets/settings';

	.header-des {
		font-size: 12px;
		line-height: 16px;
		&__top {
			position: relative;
			white-space: nowrap;
		}
		&__city {
			text-overflow: ellipsis;
			overflow: hidden;
			transition: .2s;
			vertical-align: top;
			&-wrapper {
				max-width: 18%;
			}
			&_read-only {
				color: #000000;
				pointer-events: none;
			}
			&-caption {
				margin-right: 5px;
				vertical-align: top;
				color: #7f7f7f;
			}
		}
		&__address {
			&-wrapper {
				max-width: 26.9%;
				white-space: normal;
				line-height: 1.1;
			}
		}
		&__accent {
			display: inline-block;
			transition: .2s;
			box-shadow: 0 1px #ef781f;
			line-height: 16px;
			&:hover {
				box-shadow: 0 1px #000000;
			}
		}
		&__phone {
			font-weight: 700;
			font-size: 17px;
		}
		&__contact {
			font-size: 20px;
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
		&__decor-tree {
			position: absolute;
			top: 17px;
			right: 275px;
			width: 39px;
			height: 60px;
			background: center / 39px 60px no-repeat url(~assets/images/xmastree.png);
			@media (-webkit-min-device-pixel-ratio: 2), 
			(min-resolution: 192dpi) {
				background: center / 39px 60px no-repeat url(~assets/images/2xmastree.png);
			}
		}
		&__decor-garland {
			position: absolute;
			top: 0;
			left: -40px;
			width: calc(100% + 80px);
			height: 17px;
			background:  center top / 1080px 17px no-repeat url(~assets/images/garland-des.png);
			@media (-webkit-min-device-pixel-ratio: 2), 
			(min-resolution: 192dpi) {
				background: center top / 1080px 17px no-repeat url(~assets/images/2garland-des.png);
			}
		}
	}
</style>
