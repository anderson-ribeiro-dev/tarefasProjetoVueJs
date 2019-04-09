<template>
	<div id="app">
		<h1>Tarefas</h1>
		<TasksProgress :progress="progress"/>
		<NewTask @taskAdded="addTask" /> <!-- recebe o index -->
		<TaskGrid :tasks="tasks"
				  @taskDeleted="deleteTask" 
				  @taskStateChanged="toggleTaskState" 
				  />
	</div>
</template>

<script>
import TasksProgress from './components/TaskProgress'
import NewTask from './components/NewTask.vue'
import TaskGrid from './components/TaskGrid.vue'
export default {
	components: { TasksProgress, TaskGrid, NewTask},
	data() {
		return {
			tasks: [
				// {name: 'Lavar a Louça', pending: false},
				// {name: 'Comprar blusa', pending: true},
				
			]
		}
	},
	computed: {
		progress(){
			const total = this.tasks.length
			const done = this.tasks.filter(t => !t.pending).length
			return Math.round(done / total * 100) || 0
		}
	},
	watch: {
		tasks: {
			deep: true, // monitora o estado interno dos elementos(permanece igual após o reflash)
			handler(){
				localStorage.setItem('tasks', JSON.stringify(this.tasks))// item no localstorage
			}
		}
	},
	methods: {
		addTask(task){
			// verificar nome duplicado 
			const sameName = t => t.name === task.name
			const reallyNew = this.tasks.filter(sameName).length == 0 // valor novo
			// verificar se o elemento é novo
			if(reallyNew){
				this.tasks.push({
					name: task.name,
					pending: task.pending || true
				})
			}

			// reallyNew && this.tasks.push({
			// 	name: task.name,
			// 	pending: task.pending || true
			// })

		},
		deleteTask(i){
			this.tasks.splice(i, 1) // excluir elemento apartir do index
		},
		toggleTaskState(i){
			this.tasks[i].pending = !this.tasks[i].pending
		}
	},
	created() { // ler dados do localstorage
		const  json = localStorage.getItem('tasks') // persisti dados
		//this.tasks = JSON.parse(json) || [] // transformar em json

		const array = JSON.parse(json)
		this.tasks = Array.isArray(array) ? array : []
	},
}
</script>

<style>
	body {
		font-family: 'Lato', sans-serif;
		background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
		color: #FFF;
	}

	#app {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	#app h1 {
		margin-bottom: 5px;
		font-weight: 300;
		font-size: 3rem;
	}
</style>
