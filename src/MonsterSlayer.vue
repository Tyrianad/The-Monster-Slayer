<template>
	<div id="app" class="container py-4">
		<div class="player-recap row">
			<div class="col-6 text-center">
				<h3>{{ p1.name }}</h3>
				<div class="health-container text-left">
					<div class="health-bar text-center" :style="p1Style">{{ p1.health }}</div>
				</div>
			</div>
			<div class="col-6 text-center">
				<h3>{{ p2.name }}</h3>
				<div class="health-container text-left">
					<div class="health-bar text-center" :style="p2Style">{{ p2.health }}</div>
				</div>
			</div>
		</div>
		<br />
		<div class="actions row border">
			<div class="col-12 d-flex justify-content-center">
				<template v-if="!ended">
					<button class="alert alert-danger m-2" @click="playerAttack()">ATTACK</button>
					<button class="alert alert-success m-2" @click="playerHeal()">HEAL</button>
					<button class="alert alert-info m-2" @click="playerGiveUp()">GIVE UP</button>
				</template>
				<template v-else>
					<button class="alert alert-success m-2" @click="restart()">START AGAIN</button>
				</template>
			</div>
		</div>
		<br />
		<div class="messages row border">
			<template v-for="(message, i) in messages">
				<div class="col-12 text-center py-2" v-bind:key="i" :class="message.class">{{ message.text }}</div>
			</template>
		</div>
	</div>
</template>

<script>
export default {
	name: "app",
	data: function() {
		return {
			p1: {
				name: "You",
				health: 100
			},
			p2: {
				name: "Monster",
				health: 100
			},
			messages: [],
			ended: false
		};
	},
	computed: {
		p1Style: function() {
			return {
				width: this.p1.health + "%"
			};
		},
		p2Style: function() {
			return {
				width: this.p2.health + "%"
			};
		}
	},
	methods: {
		restart: function() {
			this.p1.health = 100;
			this.p2.health = 100;
			this.messages = [];
			this.ended = false;
		},
		generateAttack: function() {
			return Math.floor(Math.random() * 5) + 5;
		},
		heal: function() {
			return 10;
		},
		addMessage: function(msg) {
			if (this.messages.length > 12) {
				this.messages = [];
			}
			this.messages.push(msg);
		},
		playerAttack: function() {
			var dmg = this.generateAttack();
			this.p2.health -= dmg;
			if (this.p2.health <= 0) {
				this.addMessage({
					text: "MONSTER HAS FAINTED",
					class: "alert-dark"
				});
				this.ended = true;
				this.p2.health = 0;
			}
			this.addMessage({
				text: "PLAYER HITS MONSTER FOR " + dmg,
				class: "alert-success"
			});
			if (!this.ended) {
				this.botTurn();
			}
		},
		playerHeal: function() {
			if (this.p1.health < 100) {
				this.addMessage({
					text: "PLAYER HEALS",
					class: "alert-success"
				});
				this.p1.health += this.heal();
				if (this.p1.health > 100) this.p1.health = 100;
			}
			this.botTurn();
		},
		playerGiveUp: function() {
			this.addMessage({
				text: "PLAYER GAVE UP",
				class: "alert-dark"
			});
			this.ended = true;
		},
		botTurn: function() {
			switch (Math.floor(Math.random() * 5) + 1) {
				case 1:case 2:case 3:
					var dmg = this.generateAttack();
					this.p1.health -= dmg;
					if (this.p1.health <= 0) {
						this.addMessage({
							text: "PLAYER HAS FAINTED",
							class: "alert-dark"
						});
						this.ended = true;
						this.p1.health = 0;
					}
					this.addMessage({
						text: "MONSTER HITS PLAYER FOR " + dmg,
						class: "alert-danger"
					});
					break;
				case 4:
					if (this.p2.health < 100) {
						if (Math.random() > 0.7) {
							this.p2.health += this.heal();
							if (this.p2.health > 100) this.p2.health = 100;
							this.addMessage({
								text: "MONSTER HEALS",
								class: "alert-danger"
							});
						} else {
							this.addMessage({
								text: "MONSTER FAILED TO HEAL!",
								class: "alert-info"
							});
						}
					}
					break;
				case 5:
					if (Math.random() > 0.9) {
						this.addMessage({
							text: "MONSTER GAVE UP!",
							class: "alert-info"
						});
						this.ended = true;
					} else {
						this.addMessage({
							text: "MONSTER IS WAITING",
							class: "alert-info"
						});
					}
					break;
			}
		}
	}
};
</script>

<style>
.health-container {
	background: #ccc;
	width: 100%;
}
.health-bar {
	color: white;
	font-weight: bold;
	background-color: green;
	padding: 5px 0;
}
</style>