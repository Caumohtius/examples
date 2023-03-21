<template>
	<div class="section-categories">
		<s-title
			v-if="catalog.title"
			:item="catalog"
			class="mb-225"
		/>
		<b-row 
			v-if="$device.isDesktop"
			cols="4"
		>
			<template
				v-for="(item, i) in catalog.items"
			>
				<s-categories-item
					:index="i"
					:key="i"
					:item="item"
				/>
			</template>
		</b-row>
		<template v-else>
			<b-row 
				cols="2"
			>
				<template
					v-for="(item, i) in catalog.items"
				>
					<s-categories-item
						v-if="i <= 6"
						:index="i"
						:key="i"
						:item="item"
					/>
					<s-categories-item
						v-else-if="!isItemsHidden"
						:index="i"
						:key="i"
						:item="item"
					/>
				</template>
			</b-row>
			<b-button
				v-if="isItemsHidden"
				variant="orange-pill"
				pill
				class="mt-4 mx-auto d-block"
				@click="isItemsHidden = false"
			>
				Показать все
			</b-button>
		</template>
	</div>
</template>

<script>
	
export default {
	props: ['catalog'],
	data: () => ({
		isItemsHidden: true,
	}),
}
	
</script>

<style lang="scss">

</style>
