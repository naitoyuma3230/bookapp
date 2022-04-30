<template>
	<div>
		<v-row>
			<v-col cols="12">
				<v-card class="mx-auto">
					<v-row>
						<v-col cols="4">
							<v-img :src="book.image"></v-img>
						</v-col>
						<v-col cols="8">
							<v-card-title> タイトル:{{ book.title }} </v-card-title>
							読んだ日:
							<v-menu
								v-model="menu"
								:close-on-content-click="false"
								:nudge-right="40"
								transition="scale-transition"
								offset-y
								min-width="auto"
							>
								<template v-slot:activator="{ on, attrs }">
									<v-text-field
										v-model="date"
										prepend-icon="mdi-calendar"
										readonly
										v-bind="attrs"
										v-on="on"
									></v-text-field>
								</template>
								<v-date-picker
									v-model="date"
									@input="menu = false"
									locale="jp-ja"
									:day-format="(date) => new Date(date).getDate()"
								></v-date-picker>
							</v-menu>
							感想:
							<v-textarea class="mx-2" v-model="book.memo">
								{{ book.memo }}
							</v-textarea>
							<v-card-actions>
								<v-btn color="secondary">一覧に戻る</v-btn>
								<v-btn color="info" @click="updateBookInfo">保存する</v-btn>
							</v-card-actions>
						</v-col>
					</v-row>
				</v-card>
			</v-col>
		</v-row>
	</div>
</template>

<script>
export default {
	name: "BookEdit",
	props: {
		books: Array,
	},
	data() {
		return {
			book: "",
			date: "",
			menu: false,
		};
	},
	// ルートが確立する前のイベントを設定
	// Vueは各コンポーネントが非同期で処理を実行するため、propsにbooksが渡る(App.vueによる)前に
	// タイトル：books[id].titleが読み込まれる($routerによる)と読み込まれない事がある
	// 画面の移動($router) /search -> /edit/:id
	// データの流れ(各component) BookSearch(子) -> App(親) -> BookEdit(子)
	beforeRouteEnter(to, from, next) {
		// next()にインスタンスを渡し処理を実行してからルーティングを行う
		// この処理はこのコンポ―ネントが作られる直前の処理となる
		// そのためこのインスタンスを指すthisは使用できない。代わりにコールバック関数に引数として作られる予定のインスタンスが渡される
		next((vm) => {
			vm.$nextTick(() => {
				vm.book = vm.books[vm.$route.params.id];
				if (vm.book.readDate) {
					vm.date = vm.book.readDate;
				} else {
					vm.date = new Date().toISOString().substr(0, 10);
				}
			});
		});
	},
	methods: {
		updateBookInfo() {
			this.$emit("update-book-info", {
				id: this.$route.params.id,
				readDate: this.date,
				memo: this.book.memo,
			});
		},
	},
};
</script>

<style></style>
