<template>
	<div>
		<form id="app" @submit.prevent="onSubmit">
            <p v-if="errors.length"> 
                <b>Please correct the following error(s):</b>
                <ul>
                <li v-for="error in errors" :key="error">{{ error }}</li>
                </ul>
            </p>

			<p>
				<label for="type">Type</label>
				<input id="type" v-model="formData.type" type="text" name="type" />
			</p>

			<p>
				<label for="description">Description</label>
				<input
					id="description"
					v-model="formData.description"
					type="text"
					name="description"
				/>
			</p>

			<p>
				<label for="fine">Fine</label>
				<input
					id="fine"
					v-model="formData.fine"
					type="number"
					name="fine"
					min="0"
				/>
			</p>

			<p>
				<label for="isPaid">
					<input type="checkbox" id="isPaid" v-model="formData.isPaid" />isPaid
				</label>
			</p>

			<p>
				<input @click="clearForm" type="button" value="Cancel" />
				<input type="submit" :value="submitTextBtn" />
			</p>
			<table>
				<thead>
					<tr>
						<th style="display:none">id</th>
						<th>Type</th>
						<th>Description</th>
						<th>Fine</th>
						<th>Paid</th>
						<th>Action</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="ticket in tickets" :key="ticket.id">
						<td style="display:none">{{ ticket.id }}</td>
						<td>{{ ticket.type }}</td>
						<td>{{ ticket.description }}</td>
						<td>{{ ticket.fine }}</td>
						<td>{{ ticket.isPaid }}</td>
						<td>
							<input type="button" @click="onEdit(ticket)" value="Edit" />
							<input
								type="button"
								@click="onDelete(ticket.id)"
								value="Delete"
							/>
							<input type="button" @click="onPaid(ticket.id)" value="Paid" />
						</td>
					</tr>
				</tbody>
			</table>
		</form>
	</div>
</template>
<script>
export default {
	data() {
		return {
			formData: {
				id: 0,
				type: "",
				description: "",
				fine: 0,
				isPaid: false,
			},
			uid: 0,
			editId: null,
			submitTextBtn: "Add",
            tickets: [],
            errors: []
		};
	},
	watch: {
		editId() {
			if (this.editId === null) {
				this.submitTextBtn = "Add";
			} else {
				this.submitTextBtn = "Update";
			}
		},
	},
	methods: {
		genUid() {
			return this.uid++;
		},
		onSubmit() {
            if(this.checkForm()){
                if (this.editId === null) {
                    this.createTicket();
                } else {
                    this.updateTicket();
                }
                this.clearForm();
                this.editId = null;
            }
        },
        checkForm() {
			if (this.formData.type && this.formData.description) {
				return true;
			}

			this.errors = [];

			if (!this.formData.type) {
				this.errors.push("Type required.");
			}
			if (!this.formData.description) {
				this.errors.push("Description required.");
            }
            return false;
		},
		createTicket() {
			const obj = this.formData;
			obj.id = this.genUid();
			this.tickets.push(obj);
		},
		updateTicket() {
			this.tickets.forEach((obj) => {
				if (obj.id === this.editId) {
					obj.type = this.formData.type;
					obj.description = this.formData.description;
					obj.fine = this.formData.fine;
					obj.isPaid = this.formData.isPaid;
				}
			});
		},
		onEdit(ticket) {
			this.formData.id = ticket.id;
			this.formData.type = ticket.type;
			this.formData.description = ticket.description;
			this.formData.fine = ticket.fine;
			this.formData.isPaid = ticket.isPaid;
			this.editId = ticket.id;
		},
		onDelete(_id) {
			this.tickets = this.tickets.filter((obj) => {
				return obj.id !== _id;
			});
		},
		onPaid(_id) {
			this.tickets.forEach((obj) => {
				if (obj.id === _id) {
					obj.isPaid = !obj.isPaid;
				}
			});
		},
		clearForm() {
			this.formData = {
				id: null,
				type: "",
				description: "",
				fine: 0,
				isPaid: false,
			};
            this.editId = null;
            this.errors = [];
		},
	},
};
</script>

<style scoped>
table {
	font-family: "Open Sans", sans-serif;
	width: 750px;
	border-collapse: collapse;
	border: 3px solid #44475c;
	margin: 10px 10px 0 10px;
}

table th {
	text-transform: uppercase;
	text-align: left;
	background: #44475c;
	color: #fff;
	padding: 8px;
	min-width: 30px;
}

table td {
	text-align: left;
	padding: 8px;
	border-right: 2px solid #7d82a8;
}
table td:last-child {
	border-right: none;
}
table tbody tr:nth-child(2n) td {
	background: #d4d8f9;
}
</style>
