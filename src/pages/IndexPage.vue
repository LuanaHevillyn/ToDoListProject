<template>
  <q-page>
    <div class="q-pa-md row flex-center absolute-center">
      <q-card bordered class="my-card shadow-4" :grid="$q.screen.xs">
        <q-card-section>
          <div class="text-h5 text-center text-accent">To-do List</div>
        </q-card-section>
        <q-card-section>
          <div class="row">
            <div class="col">
              <q-input
                clearable
                clear-icon="close"
                outlined
                color="purple"
                v-model="taskInput"
                label="Ex: Praticar natação"
              />
            </div>
            <div class="col-grow">
              <q-btn
                unelevated
                padding="md"
                color="accent"
                label="Adicionar"
                @click="createTask(taskInput)"
              />
            </div>
          </div>
        </q-card-section>

        <q-separator inset />
        <q-card-section>
          <div class="text-center">
            <q-checkbox
              left-label
              label="Filtrar concluídos: "
              name="seeTasksCheckbox"
              v-model="seeCompletedTasks"
              color="accent"
            />
          </div>
          <q-table
            flat
            bordered
            :rows="filteredTasks"
            :columns="columns"
            row-key="name"
            selection="single"
            v-model:selected="selected"
            :dense="$q.screen.lt.md"
          >
            <template v-slot:body="props">
              <q-tr :props="props">
                <q-td>
                  <q-checkbox
                    :name="'checkbox' + props.row"
                    v-model="props.row.status"
                    :true-value="Status.Concluído"
                    :false-value="Status.Pendente"
                    color="accent"
                  />
                </q-td>
                <q-td key="selected" :props="props">
                  {{ props.row.name }}
                </q-td>
                <q-td key="name" :props="props">
                  {{ props.row.name }}
                </q-td>
                <q-td key="status" :props="props">
                  <q-badge :color="changeStatusColor(props.row.status)">
                    {{ props.row.status }}
                  </q-badge>
                </q-td>
                <q-td key="actions" :props="props">
                  <q-btn unelevated @click="deleteTask(props.row)">
                    <q-icon size="2em" name="delete" color="red" />
                  </q-btn>
                </q-td>
              </q-tr>
            </template>
            <template v-slot:no-data="{ message }">
              <div class="full-width row flex-center text-accent q-gutter-sm">
                <q-icon size="2em" name="sentiment_dissatisfied" />
                <span>{{ message }}</span>
              </div>
            </template>
          </q-table>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { QTableColumn } from 'quasar';
import { computed, ref } from 'vue';

const taskInput = ref('');
const selected = ref([]);
const tasks = ref<TaskObject[]>([]);
const seeCompletedTasks = ref(false);

enum Status {
  Pendente = 'Pendente',
  Concluído = 'Concluído',
}

interface TaskObject {
  name: string;
  status: Status;
}

function createTask(taskName: string): void {
  if (taskName.trim() !== '') {
    tasks.value.push({
      name: taskName,
      status: Status.Pendente,
    });
    taskInput.value = '';
  }
}

function deleteTask(index: number): void {
  tasks.value.splice(index, 1);
}

function changeStatusColor(taskStatus: Status): string {
  if (taskStatus === Status.Concluído) {
    return 'green';
  }
  return 'orange';
}

const columns: QTableColumn[] = [
  {
    name: 'name',
    required: true,
    label: 'Tarefa',
    align: 'left',
    field: 'name',
    sortable: true,
  },
  {
    name: 'status',
    align: 'center',
    label: 'Status',
    field: 'status',
    sortable: true,
  },
  {
    name: 'actions',
    align: 'center',
    label: '',
    field: 'actions',
    sortable: true,
  },
];

const filteredTasks = computed(() => {
  return seeCompletedTasks.value
    ? tasks.value.filter((task) => task.status === Status.Concluído)
    : tasks.value;
});
</script>
