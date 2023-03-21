<template>
	<div class="header-panel"
		v-if="header"
		@click="headerClick"
		@mousemove.passive="headerMove"
	>
		<div class="header-panel__wrapper rounded mx-auto">
			<b-container class="header-panel__container d-flex align-items-center py-3">

				<kms-link to="/" type="" class="mr-425">
					<img
						src="~/assets/images/logo.svg"
						:alt="header.logo_alt"
						class="header-panel__logo"
					/>
				</kms-link>

				<b-button
					pill
					:variant="!isDropdownVisible ? 'orange-pill' : 'black-pill'"
					class="header-panel__catalog-btn mr-35"
					@mouseover="setDropdownVisible(true)"
				>
					<IconCatalog
						v-if="!isDropdownVisible"
                        scale=".8"
                        class="header-panel__catalog-btn-icon mr-225"
                    />
					<IconCross
                        v-else
                        class="header-panel__catalog-btn-icon mr-225"
                    />
					{{ header.catalog.title }}
				</b-button>

				<s-search-fast-des
					ref="searchFast"
					v-if="isSearchFastActive"
					v-click-outside="hideSearchFast"
					@choosed="isSearchFastActive = false"
				/>

				<template v-if="!isSearchFastActive">
					<kms-link
						v-for="(item, i) in header.links"
						:to="item.url"
						:type="item.type"
						:key="i"
						class="header-panel__link"
						:class="{
							'header-panel__link_active' : page.resource_type === item.type
						}"
					>
						{{ item.title }}
					</kms-link>
				</template>

				<div class="header-panel__menu d-flex ml-auto">
					<div
						class="header-panel__menu-link_search header-panel__menu-link header-panel__menu-link_accent d-flex align-items-center justify-content-center cursor-pointer"
						data-test-id="header-panel-des__btn_search"
						v-if="!isSearchFastActive"
						@click="showSearchFast"
					>
						<IconSearch />
					</div>
					<kms-link
						v-if="header.compare"
						:to="header.compare.url"
						:type="header.compare.type"
						class="header-panel__menu-link position-relative d-flex align-items-center justify-content-center"
						data-test-id="header-panel-des__btn_compare"
					>
						<IconCompare />
						<span
							class="header-panel__counter d-flex align-items-center justify-content-center position-absolute"
							:class="{'header-panel__counter_big' : compare.count > 9}"
							v-if="compare.count"
						>
							{{ compare.count <= 999 ? compare.count : '99' }}
						</span>
					</kms-link>
					<kms-link
						v-if="header.favorite"
						:to="header.favorite.url"
						:type="header.favorite.type"
						class="header-panel__menu-link position-relative d-flex align-items-center justify-content-center"
						data-test-id="header-panel-des__btn_favorite"
					>
						<IconBookmark />
						<span
							class="header-panel__counter d-flex align-items-center justify-content-center position-absolute"
							:class="{'header-panel__counter_big' : favorite.count > 9}"
							v-if="favorite.count"
						>
							{{ favorite.count <= 999 ? favorite.count : '99' }}
						</span>
					</kms-link>
					<kms-link
						v-if="header.cart"
						:to="header.cart.url"
						:type="header.cart.type"
						class="header-panel__menu-link position-relative d-flex align-items-center justify-content-center"
						data-test-id="header-panel-des__btn_cart"
					>
						<IconCart />
						<span
							class="header-panel__counter d-flex align-items-center justify-content-center position-absolute"
							:class="{'header-panel__counter_big' : cart.count > 9}"
							v-if="cart.count"
						>
							{{ cart.count <= 999 ? cart.count : '99' }}
						</span>
					</kms-link>

				</div>
			</b-container>
		</div>

		<transition name="fade">
			<s-header-dropdown-menu-des
				v-if="isDropdownVisible && header.catalog"
				:categories="header.catalog"
			/>
		</transition>

	</div>
</template>

<script>
import { mapState, mapMutations } from "vuex";
	
export default {
	data: () => ({
		isSearchFastActive: false
	}),
	computed: {
		...mapState('header', {
			'header': 'data'
		}),
		...mapState('page', {
			'page': 'data'
		}),
		...mapState('cart', {
			'cart': 'data'
		}),
		...mapState('favorite', {
			'favorite': 'data'
		}),
		...mapState('compare', {
			'compare': 'data'
		}),
		...mapState('headerPanel', ['isDropdownVisible']),
	},
	methods: {
		...mapMutations('headerPanel', [ 'setDropdownVisible' ]),
		hideDropdown () {
			this.setDropdownVisible(false)
		},
		headerMove ($event) {
			const target = $event.target;
			if (
				// если навели не на панель в шапке
				!target.closest('.header-panel__container')
				// и не на выпадающий блок
				&& !target.closest('.section-header-dropdown-menu-des__card')
				|| (
					// навели внутри блока, но это не кнопка каталоге
					target.closest('.header-panel__container')
					// не панель
					&& !target.classList.contains('header-panel__container')
					// не кнопка
					&& !target.closest('.header-panel__catalog-btn')
				)
			) {
				this.hideDropdown();
			}
		},
		headerClick ($event) {
			const target = $event.target;
			if (
				// или кликнули на ссылку внутри выпадающего меню
				target.closest('.section-header-dropdown-menu-des')
				&& target.tagName === 'A'
			) {
				this.hideDropdown();
			}
		},
		hideSearchFast (e) {
			const target = e.target;
			if (
				!target.closest('.header-panel__menu-link_search')
			) {
				this.isSearchFastActive = false;
			}
		},
		showSearchFast (e) {
			this.isSearchFastActive = true;
			this.$nextTick(() => {
				const input = this.$_.get(this, '$refs.searchFast.$refs.input');
				if (input) {
					input.focus();
				}
			});
		}
	} 
}
	
</script>

<style lang="scss">

	@import '~/assets/settings';

	.header-panel {
		position: sticky;
		z-index: $zindex-sticky;
		top: 20px;
		left: 0;
		right: 0;
		&__wrapper {
			width: max-content;
			background-color: #ffffff;
			border: 1px solid #F0F0F0;
			box-shadow: 4px 4px 40px rgba(0, 0, 0, .05);	
		}

		&__logo {
			width: 60px;
			height: 42px;
		}
		&__catalog-btn {
			white-space: nowrap;
			font-size: 16px;
		}
		&__link {
			position: relative;
			margin-right: 15px;
			white-space: nowrap;
			@include media-breakpoint-up(xl) {
				margin-right: 30px;
			}
			&::before {
				content: "";
				opacity: 0;
				position: absolute;
				top: -32px;
				left: 0;
				border-top: 4px solid #ef781f;
				width: 100%;
				border-radius: 1rem;
				transition-duration: .2s;
			}
			&:hover::before, &_active::before {
				opacity: 1;
			}
			&_active {
				color: #ef781f;
				pointer-events: none;
			}
		}

		&__menu {
			&-link {
				width: 48px;
				height: 48px;
				// margin-right: 10px;
				border-radius: 50%;
				transition: color .2s, background-color .2s;
				line-height: 1;
				& + & {
					box-shadow: inset 0 0 0 1px rgba(0, 0, 0, .1);
				}
				&_accent {
					color: #ffffff;
					background-color: #ef781f;
					box-shadow: none;
				}
				&:hover {
					background-color: #000000;
					color: #ffffff;
					box-shadow: none;
				}
				&:not(:last-child) {
					margin-right: 10px;
				}
			}			
		}
		&__counter {
			top: 2%;
			right: 2%;
			width: 1.125rem;
			height: 1.125rem;
			font-size: 10px;
			font-weight: 700;
			color: #ffffff;
			background-color: #ef781f;
			border-radius: 50%;
			white-space: nowrap;
			box-shadow: 0 0 0 4px #ffffff;
			transform: translate(25%, -25%);
			&_big {
				width: 1.5rem;
				height: 1.5rem;
			}
		}
	}

</style>
