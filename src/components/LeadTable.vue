<template>
  <div class="w-full mt-10 px-4">
    <div class="flex justify-between bg-white mt-4 mb-2 pl-2">
      <h3 class="text-lg">Пример тестового задания Плющ Максим</h3>
      <div class="w-[20rem]">
        <a-input-search
          placeholder="Введите текст для поиска"
          enter-button="Поиск"
          :scroll="{ x: 'max-content' }"
          @search="fetchLeads"
          v-model="searchQuery"
          :loading="isFetching"
          allow-clear
        />
      </div>
    </div>
    <div class="w-ful overflow-scroll">
      <a-table
        :columns="columns"
        :data-source="leads"
        :row-key="(record) => record.id"
        :pagination="{ hideOnSinglePage: true }"
        :loading="isFetching"
      >
        <template #expandedRowRender="{ record }">
          <div class="flex gap-x-2 items-center">
            <p style="margin: 0">
              {{ record.contact?.name || "Без контакта" }}
            </p>
            <div class="flex gap-x-1 text-lg" v-if="record.contact?.name">
              <a
                class="hover:opacity-[70%]"
                :href="'tel:' + record.contact?.phone"
                >📱</a
              >
              <a
                class="hover:opacity-[70%]"
                :href="'mailto:' + record.contact?.email"
                >✉️</a
              >
            </div>
          </div>
        </template>
      </a-table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import dayjs from "dayjs";

export default {
  data() {
    return {
      leads: [],
      searchQuery: "",
      isFetching: false,
      columns: [
        {
          title: "Название",
          dataIndex: "name",
          key: "name",
        },
        {
          title: "Бюджет",
          dataIndex: "price",
          key: "price",
        },
        {
          title: "Статус",
          dataIndex: ["status", "name"],
          key: "status",
        },
        {
          title: "Ответственный",
          dataIndex: ["user", "name"],
          key: "user",
        },
        {
          title: "Дата создания",
          dataIndex: "created_at",
          key: "created_at",
          customRender: ({ text }) =>
            dayjs.unix([text]).format("DD.MM.YYYY HH:mm"),
        },
      ],
    };
  },
  methods: {
    fetchLeads(query = "") {
      this.isFetching = true;

      const url = query
        ? `http://localhost:3000/leads?query=${query}`
        : "http://localhost:3000/leads";

      axios.get(url).then((response) => {
        this.leads = response.data;
        this.isFetching = false;
      });
    },
  },
  mounted() {
    this.fetchLeads();
  },
};
</script>
