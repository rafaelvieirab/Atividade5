<template>
    <main>
        <fieldset>
            <legend>Form</legend>
            <form>
                <div class="input-group">
                    <label for="id">Id</label>
                    <input type="number" id="id" v-model="id" min="0" :max="recordsNumber-1">
                </div>
                <div class="input-group">
                    <label for="name">Nome</label>
                    <input type="text" id="name" v-model="name" placeholder="Digite seu nome">
                </div>
                <div class="input-group">
                    <label for="office">Cargo</label>
                    <input type="text" id="office" v-model="office" placeholder="Digite seu cargo">
                </div>
                <div class="input-group">
                    <label for="birthDate">Data de Nascimento</label>
                    <input type="date" id="birthDate" v-model="birthDate" min="1950-01-01" max="2020-11-01">
                </div>
                <div class="input-group">
                    <label for="entryDate">Data de Entrada na Empresa</label>
                    <input type="date" id="entryDate" v-model="entryDate"  min="1950-01-01" max="2020-11-01">
                </div>

                <div class="buttons-form">
                    <div class="row1">
                        <button type="reset">Limpar</button>
                        <button @click.prevent="updateEmployee">Atualizar</button>
                        <button @click.prevent="addEmployee">Cadastrar</button>
                    </div>
                    <div class="row2">
                        <button @click.prevent="getAll">Buscar Todos</button>
                        <button @click.prevent="getById">Buscar pelo Id</button>
                        <button @click.prevent="deleteEmployeeById">Deletar</button>
                    </div>
                </div>
            </form> 
        </fieldset>
        
        <fieldset class="filter-container">
            <legend>Filtros</legend>
            <div class="filter">
                <label for="limit">Número de Funcionários: </label>
                <input type="number" v-model="limit" id="limit" min="0" :max="recordsNumber-1">
            </div>
            <div class="filter">
                <label for="filterOffice">Cargo: </label>
                <input type="text" v-model="filterOffice" id="filterOffice">
            </div>
            <div class="btn-filter">
                <button @click="getFiltered">Aplicar filtros</button>
            </div>
        </fieldset>

        <section id="result">
            <h3>Resultado das Busca</h3>
            <ul>
                <li v-for="employee in employees" v-bind:key="employee.id">
                    <p>Id: {{employee.id}}</p>
                    <p>Nome: {{employee.name}}</p>
                    <p>Cargo: {{employee.office}}</p>
                    <p>Data de Nascimento: {{employee.birthDate}}</p>
                    <p>Data de Entrada na Empresa: {{employee.entryDate}}</p>
                </li>
            </ul>
        </section>

    </main>
</template>

<script>

import baseURL from '../urlServer.js'

export default {
    name: "Employers",
    data() {
        return {
            id: 0,
            limit: 0,
            filterOffice: "",
            name: "",
            office: "",
            birthDate:"",
            entryDate:"",
            employee: {},
            employees: [],
            recordsNumber: 0,
        }
    },
    created: function() {
        this.getAll()
    },
    methods: {
        getAll: function() {
            this.$http.get(baseURL).then(result =>{
                this.employees = result.data
                this.recordsNumber = this.employees.length
            })
        },
        getFiltered: function() {
            if(this.filterOffice === "") {
                alert("Digite algo no filtro de Cargo")
                return
            }
            this.$http.get(
                `${baseURL}?office=${this.office}&limit=${this.limit}`
            ).then(result => {
                this.employees = result.data
            })
        },
        getById: function() {
            this.$http.get(baseURL+'/'+this.id).then(result => {
                this.employees = [result.data]
            })
        },
        addEmployee: function() {
            if(!this.fieldValidation())
                return
            
            let employee = {
                name: this.name,
                office: this.office,
                birthDate: this.birthDate,
                entryDate: this.entryDate
            }
            this.$http.post(baseURL, employee).then(result => {
                console.log(result.data)
                this.getAll()
            })
        },
        updateEmployee: function() {
            if(!this.fieldValidation())
                return

            let employee = {
                id: this.id,
                name: this.name,
                office: this.office,
                birthDate: this.birthDate,
                entryDate: this.entryDate
            }
            this.$http.put(baseURL+'/'+this.id, employee).then(result => {
                console.log(result.data)
                this.getAll()
            })
        },
        deleteEmployeeById: function() {
            this.$http.delete(baseURL+'/'+this.id).then(result => {
                console.log("Funcionário Deletado")
                this.getAll()
            })
        },
        fieldValidation: function() {
            if(this.name === "" || this.office === "" | this.birthDate === "" | this.entryDate === "") {
                alert("Preencha todos os campos!")
                return false
            }
            return true;
        }
    }
}
</script>

<style>

button {
    height: 36px;
    padding: 10px;
    margin: 5px;

    background-color: #e3e3e3;
    color: #333333;
    border-radius: 10px;
    
    cursor: pointer;

    transition: 0.2s;
    border: none;
}

button:hover {
    background-color: #f86e5c;
    color: #ffffff;
    opacity: 80%;
    transform: scale(1.05, 1.05);
}

main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

fieldset {
    padding: 10px;
    margin: 8px;
}

form {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.input-group {
    margin-bottom: 5px;
    display: flex;
    width: 100%;
    align-items: center;
    justify-content: space-between;
}

.input-group label {
    text-align: center;
}

.input-group input {
    min-width: 230px;
    text-align: center;
}

.buttons-form {
    padding: 10px;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
}

.buttons-form .row1,.row2 {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.filter-container .filter {
    width: 100%;
    margin-bottom: 10px;
}

.filter > label {
    display: inline-block;
    min-width: 225px;
}

.filter-container .btn-filter {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
}

li {
    list-style: none;
    padding: 5px;
}
</style>