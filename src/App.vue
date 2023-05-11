<template>
  <v-app>
    <v-img
        src="https://spa-bitrix.ru/engeo-development/form/img_front/front.jpg"
    >
      <v-main>
        <v-container>
          <v-row>
            <v-col>
              <v-card style="color: #B0857C">
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-card-title>Регистрация клиента за агентством недвижимости</v-card-title>
                    </v-col>
                    <v-col>
                      <v-row>
                        <v-btn
                            class="ma-0"
                            text
                            icon
                            color="#B0857C"
                            @click="infoMenu = true"
                        >
                          <v-icon>mdi-information</v-icon>
                        </v-btn>
                        <p class="ma-1">инструкция</p>
                      </v-row>
                    </v-col>
                  </v-row>
                </v-container>
                <template>
                  <v-dialog
                      v-model="infoMenu"
                      width="600px"
                  >
                    <v-card>
                      <v-card-title class="text-subtitle-2">Инструкция по использованию</v-card-title>
                      <v-card-text class="text-body-2">
                        <div v-html="textInfo"></div>
                      </v-card-text>
                    </v-card>
                  </v-dialog>
                </template>
                <v-form class="ma-1 pa-2" ref="form" v-model="valid">
                  <v-text-field
                      v-model="name"
                      label="ФИО клиента"
                      :rules="[v => !!v || 'ФИО клиента обязательно к заполнению!']"
                      required
                      dense
                      outlined
                  ></v-text-field>
                  <div>
                    <VuePhoneNumberInput

                        v-model="phone"
                        :rules="[v => !!v || 'Телефон клиента обязателен к заполнению!']"
                        @update="clientPhoneValidMethod"
                        required
                        :error="clientPhoneValidTrue === false"
                        default-country-code="RU"
                        :translations="{
                                    countrySelectorLabel: 'Код страны',
                                    example: 'Пример :',
                                    phoneNumberLabel: 'Телефон клиента',
                                   }"
                    />
                    <p v-if="clientPhoneValidTrue === false" class="pl-3" style="color: #F74343"><small>Телефон клиента обязателен к
                      заполнению!</small></p>
                  </div>

                  <v-text-field
                      class="pt-7"
                      @blur="focusOut"
                      @focus="focusIn"
                      v-model="dealSum"
                      label="Бюджет клиента"

                      :rules="[v => !!v || 'Бюджет клиента обязателен к заполнению!']"
                      required
                      dense
                      outlined
                  ></v-text-field>
                  <v-select
                      v-model="projectSelect"
                      :items="projects"
                      item-text="TITLE"
                      item-value="ID"
                      label="Проект"
                      :rules="[v => !!v || 'Проект обязателен к заполнению!']"
                      required
                      dense
                      outlined
                  >
                  </v-select>
                  <v-divider class="mb-3 pb-3"></v-divider>
                  <v-text-field
                      v-model="brockerName"
                      label="ФИО брокера"
                      :rules="[v => !!v || 'ФИО брокера обязательно к заполнению!']"
                      required
                      dense
                      outlined
                  ></v-text-field>
                  <div>
                    <VuePhoneNumberInput
                        v-model="brockerPhone"
                        required
                        :error="brockerPhoneValidTrue === false"
                        @update="brockerPhoneValidMethod"
                        default-country-code="RU"
                        :translations="{
                                    countrySelectorLabel: 'Код страны',
                                    example: 'Пример :',
                                    phoneNumberLabel: 'Телефон брокера',
                                   }"
                    />
                    <p v-if="brockerPhoneValidTrue === false" class="pl-3" style="color: #F74343"><small>Телефон брокера обязателен к
                      заполнению!</small></p>
                  </div>
                  <v-autocomplete
                      class="pt-7"
                      v-model="brockerSelect"
                      :items="brockers"
                      item-text="TITLE"
                      item-value="ID"
                      label="Агентство (введите ИНН или название)"
                      :rules="[v => !!v || 'Агентство обязательно к заполнению!']"
                      :filter="filterToInn"
                      required
                      dense
                      outlined
                  ></v-autocomplete>
                  <v-btn class="text-button" color="#B0857C" text @click="openBrockerAdd">
                    <v-icon>mdi-account-multiple-plus</v-icon>
                    &nbsp; Добавить новое агентство
                  </v-btn>
                  <template>
                    <v-dialog
                        v-model="menu"
                        fullscreen
                        hide-overlay
                        transition="dialog-bottom-transition"
                        scrollable
                    >
                      <v-img
                          src="https://spa-bitrix.ru/engeo-development/form/img_front/front.jpg"
                      >
                        <v-container fluid>
                          <v-card style="color: #B0857C">
                            <v-card-title>Регистрация агентства</v-card-title>
                            <v-form class="ma-2 pa-2" ref="formAgency" v-model="validAgency">
                              <v-text-field
                                  class="mb-8"
                                  v-model="titleAgency"
                                  label="Название агентства"
                                  :rules="[v => !!v || 'Название агентства обязательно к заполнению!']"
                                  required
                                  hide-details="auto"
                                  dense
                                  outlined
                              ></v-text-field>
                              <div>
                                <VuePhoneNumberInput
                                    v-model="phoneAgency"
                                    :rules="[v => !!v || 'Телефон агентства обязательно к заполнению!']"
                                    required
                                    :error="agencyPhoneValidTrue === false"
                                    @update="agencyPhoneValidMethod"
                                    default-country-code="RU"
                                    :translations="{
                                    countrySelectorLabel: 'Код страны',
                                    example: 'Пример :',
                                    phoneNumberLabel: 'Телефон агентства',
                                   }"
                                />
                                <p v-if="agencyPhoneValidTrue === false" class="pl-3" style="color: #F74343"><small>Телефон агентства обязателен к
                                  заполнению!</small></p>
                              </div>
                              <v-text-field
                                  class="pt-7"
                                  v-model="innAgency"
                                  label="ИНН агентства"
                                  :rules="[v => !!v || 'ИНН агентства обязательно к заполнению!']"
                                  required
                                  dense
                                  outlined
                              ></v-text-field>

                              <v-divider class="mb-3 pb-3"></v-divider>

                              <v-text-field
                                  v-model="brockerName"
                                  label="Контактное лицо"
                                  :rules="[v => !!v || 'ФИО контактного лица обязательно к заполнению!']"
                                  required
                                  dense
                                  outlined
                              ></v-text-field>
                              <div>
                                <VuePhoneNumberInput
                                    v-model="brockerPhone"
                                    required
                                    :error="brockerPhoneValidTrue === false"
                                    @update="brockerPhoneValidMethod"
                                    default-country-code="RU"
                                    :translations="{
                                    countrySelectorLabel: 'Код страны',
                                    example: 'Пример :',
                                    phoneNumberLabel: 'Телефон контактного лица',
                                   }"
                                />
                                <p v-if="brockerPhoneValidTrue === false" class="pl-3" style="color: #F74343"><small>Телефон контактного лица обязателен к
                                  заполнению!</small></p>
                              </div>

                              <template>
                                <v-row>
                                  <v-col cols="1"  class="pt-7">
                                    <v-checkbox
                                        v-model="checkbox"
                                        class="ma-0 pa-0"
                                        :rules="[v => !!v || '']"
                                        color="#B0857C"
                                    >
                                    </v-checkbox>
                                  </v-col>
                                  <v-col>
                                    <div class="black--text">Я согласен на <a
                                        target="_blank"
                                        @click="checkDialog=true"
                                    >
                                      обработку персональных данных
                                    </a></div>
                                  </v-col>
                                </v-row>
                              </template>
                              <v-card-actions class="ma-1" justify="space-between">
                                <v-row>
                                  <v-col class="text-left">
                                    <v-btn class="text-button" text color="#B0857C" @click="addAgency"
                                           :disabled="loadingAgensy">
                                      Сохранить
                                    </v-btn>
                                  </v-col>
                                  <v-col class="text-right">
                                    <v-btn class="text-button" text color="#B0857C" @click="openBrockerAdd">Отменить
                                    </v-btn>
                                  </v-col>
                                </v-row>
                              </v-card-actions>
                            </v-form>
                          </v-card>
                        </v-container>
                      </v-img>
                    </v-dialog>
                  </template>
                  <template>
                    <v-row class="ma-2 pa-0">
                      <v-col cols="1">
                        <v-checkbox
                            v-model="checkbox"
                            class="ma-0 pa-0"
                            :rules="[v => !!v || '']"
                            color="#B0857C"
                        >
                        </v-checkbox>
                      </v-col>
                      <v-col>
                        <div class="black--text">Я согласен на <a
                            target="_blank"
                            @click="checkDialog=true"
                        >
                          обработку персональных данных
                        </a></div>
                      </v-col>
                    </v-row>
                  </template>
                  <template>
                    <v-dialog
                        v-model="checkDialog"
                        width="600px"
                    >
                      <v-card>
                        <v-card-title class="text-subtitle-2">Согласие на обработку персональных данных</v-card-title>
                        <v-card-text class="text-body-2">
                          <div v-html="checkText"></div>
                        </v-card-text>
                        <v-card-actions class="ma-1">
                          <v-row>
                            <v-col>
                              <v-btn class="text-button" text style="color: #B0857C"
                                     @click="checkbox=true; checkDialog=false">Согласен
                              </v-btn>
                            </v-col>
                            <v-col class="text-right">
                              <v-btn class="text-button" text style="color: #B0857C" @click="checkDialog=false">Отменить
                              </v-btn>
                            </v-col>
                          </v-row>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                  </template>
                  <v-card-actions class="ma-1">
                    <v-row>
                      <v-col>
                        <v-btn class="text-button" text style="color: #B0857C" @click="dealsAdd"
                               :disabled="loadingDealAdd">Отправить
                        </v-btn>
                      </v-col>
                      <v-col class="text-right">
                        <v-btn text style="color: #B0857C" class="text-button" @click="clearDeals"
                               :disabled="loadingDealAdd">Очистить
                        </v-btn>
                      </v-col>
                    </v-row>
                  </v-card-actions>
                </v-form>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-img>
  </v-app>
</template>

<script>
import axios from 'axios'
import VuePhoneNumberInput from 'vue-phone-number-input';
import 'vue-phone-number-input/dist/vue-phone-number-input.css';

export default {
  name: 'App',
  components: {VuePhoneNumberInput},
  data: () => ({
    clientPhoneValid: true,
    clientPhoneValidTrue: true,
    brockerPhoneValid: true,
    brockerPhoneValidTrue: true,
    agencyPhoneValid: true,
    agencyPhoneValidTrue: true,
    checkbox: false,
    infoMenu: false,
    checkDialog: false,
    textInfo: `Через данную форму вы можете проверить клиента на уникальность и зафиксировать его за собой.<br>
Если в поле «Агентство» вы не можете найти свою компанию, то ее стоит добавить в ручном режиме, нажав на кнопку справа.<br>
<br>
После отправки формы на указанный мобильный телефон придет SMS с информацией об успешной регистрации, если клиент уже зафиксирован за другим агентством недвижимости система оповестит вас об этом.<br>
Далее вы можете связаться с офисом продаж, чтобы согласовать время показа для клиента.`,
    checkText: `Настоящим в соответствии с Федеральным законом № 152-ФЗ «О персональных данных» от 27.07.2006 года свободно, своей волей и в своем интересе выражаю свое безусловное согласие на обработку моих персональных данных АО "ИНГЕОЦЕНТР" (ОГРН 1027739224820, ИНН 7730150393), зарегистрированным в соответствии с законодательством РФ по адресу:<br>
БАГРАТИОНОВСКИЙ ПРОЕЗД, ДОМ 7, КОРПУС 20В, ОФИС 211, МОСКВА, Россия, 121087 (далее по тексту - Оператор).<br>
Персональные данные - любая информация, относящаяся к определенному или определяемому на основании такой информации физическому лицу.<br>
Настоящее Согласие выдано мною на обработку следующих персональных данных:<br>
- Имя;<br>
- Телефон;<br>
- E-mail.<br>
<br>
Согласие дано Оператору для совершения следующих действий с моими персональными данными с использованием средств автоматизации и/или без использования таких средств: сбор, систематизация, накопление, хранение, уточнение (обновление, изменение), использование, обезличивание, а также осуществление любых иных действий, предусмотренных действующим законодательством РФ как неавтоматизированными, так и автоматизированными способами.<br>
Данное согласие дается Оператору для обработки моих персональных данных в следующих целях:<br>
- предоставление мне услуг/работ;<br>
- направление в мой адрес уведомлений, касающихся предоставляемых услуг/работ;<br>
- подготовка и направление ответов на мои запросы;<br>
- направление в мой адрес информации, в том числе рекламной, о мероприятиях/товарах/услугах/работах Оператора.<br>
<br>
Настоящее согласие действует до момента его отзыва путем направления соответствующего уведомления на электронный адрес bk@rcgit.ru. В случае отзыва мною согласия на обработку персональных данных Оператор вправе продолжить обработку персональных данных без моего согласия при наличии оснований, указанных в пунктах 2 – 11 части 1 статьи 6, части 2 статьи 10 и части 2 статьи 11 Федерального закона №152-ФЗ «О персональных данных» от 27.07.2006 г.`,
    valid: true,
    validAgency: true,
    menu: false,
    loadingAgensy: false,
    loadingDealAdd: false,
    brockers: [],
    brockerSelect: '',
    projects: [{TITLE: 'БОЛЬШАЯ ДМИТРОВКА XI', ID: '379'}, {TITLE: 'КАМЕРГЕР', ID: '377'}],
    projectSelect: '',
    titleAgency: '',
    phoneAgency: '',
    innAgency: '',
    name: '',
    brockerName: '',
    brockerPhone: '',
    phone: '',
    dealSum: 0,
    selectManager: '',
    url: 'https://engeocentr.bitrix24.ru/rest/839/wnal269dpe5j3ew9/'
  }),
  methods: {
    clientPhoneValidMethod(data) {
      console.log(data)
      if (data.isValid === false) this.clientPhoneValid = false
      else this.clientPhoneValid = true
    },
    brockerPhoneValidMethod(data) {
      console.log(data)
      if (data.isValid === false) this.brockerPhoneValidTrue = false
      else this.brockerPhoneValidTrue = true
    },
    agencyPhoneValidMethod(data) {
      console.log(data)
      if (data.isValid === false) this.agencyPhoneValidTrue = false
      else this.agencyPhoneValidTrue = true
    },
    focusOut() {
      const value = this.dealSum;
      const formatted = new Intl.NumberFormat('ru-RU').format(value)
      this.dealSum = formatted;
    },
    focusIn() {
      if (this.dealSum !== 0) {
        const cleanValue = this.dealSum.replace(/\s+/g, '');
        const intValue = parseInt(cleanValue, 10);
        this.dealSum = 0;
        this.dealSum = intValue;
      }
    },
    validate() {
      this.$refs.form.validate()
      if (this.clientPhoneValid === false  || this.phone.length <=0) this.clientPhoneValidTrue = false
      else this.clientPhoneValidTrue = true
      if (this.brockerPhoneValid === false  || this.brockerPhone.length <=0) this.brockerPhoneValidTrue = false
      else this.brockerPhoneValidTrue = true
      if (this.agencyPhoneValid === false  || this.phoneAgency.length <=0) this.agencyPhoneValidTrue = false
      else this.agencyPhoneValidTrue = true
    },
    validateAgency() {
      this.$refs.formAgency.validate()
      if (this.clientPhoneValid === false  || this.phone.length <=0) this.clientPhoneValidTrue = false
      else this.clientPhoneValidTrue = true
      if (this.brockerPhoneValid === false  || this.brockerPhone.length <=0) this.brockerPhoneValidTrue = false
      else this.brockerPhoneValidTrue = true
      if (this.agencyPhoneValid === false  || this.phoneAgency.length <=0) this.agencyPhoneValidTrue = false
      else this.agencyPhoneValidTrue = true
    },
    async dealsAdd() {
      try {
        this.validate()
        if (this.valid) {
          this.loadingDealAdd = true
          //ищем клиента по телефону
          let clientAdd = ''
          const newPhone = this.phone.replace(/[^+\d]/g, '')
          const client = await axios.post(this.url + 'crm.duplicate.findbycomm',
              {
                entity_type: "CONTACT",
                type: "PHONE",
                values: [`+7${newPhone}`, `8${newPhone}`]
              })
          if (client.data.result.length < 1) {
            const clientAddData = await axios.post(this.url + 'crm.contact.add', {
              fields: {
                "NAME": this.name,
                "OPENED": "Y",
                "ASSIGNED_BY_ID": this.selectManager,
                "TYPE_ID": "CLIENT",
                "PHONE": [{"VALUE": `+7${this.phone}`, "VALUE_TYPE": "WORK"}]
              },
              params: {"REGISTER_SONET_EVENT": "Y"}
            })
            clientAdd = clientAddData.data.result
            // ищем брокера
            let brockerAdd = ''
            const newPhoneBrocker = this.brockerPhone.replace(/[^+\d]/g, '')
            const brocker = await axios.post(this.url + 'crm.duplicate.findbycomm',
                {
                  entity_type: "CONTACT",
                  type: "PHONE",
                  values: [`+7${newPhoneBrocker}`, `8${newPhoneBrocker}`]
                })
            if (brocker.data.result.length < 1) {
              const brockerAddData = await axios.post(this.url + 'crm.contact.add', {
                fields: {
                  "NAME": this.brockerName,
                  "OPENED": "Y",
                  "ASSIGNED_BY_ID": this.selectManager,
                  "TYPE_ID": "SUPPLIER",
                  "PHONE": [{"VALUE": `+7${this.brockerPhone}`, "VALUE_TYPE": "WORK"}],
                  'COMPANY_ID': this.brockerSelect
                },
                params: {"REGISTER_SONET_EVENT": "Y"}
              })
              brockerAdd = brockerAddData.data.result
            } else {
              brockerAdd = brocker.data.result.CONTACT[0]
              await axios.post(this.url + 'crm.contact.update', {
                id: brockerAdd,
                fields:
                    {
                      "COMPANY_ID": this.brockerSelect,
                    },
                params: {"REGISTER_SONET_EVENT": "Y"}
              })
            }
            console.log('создаем сделку')
            //создаем сделку
            const dealAdd = await axios.post(this.url + 'crm.deal.add', {
              fields:
                  {
                    "TITLE": "Заявка от брокера",
                    'TYPE_ID': 1,
                    "STAGE_ID": "C5:NEW",
                    "COMPANY_ID": this.brockerSelect,
                    "CONTACT_ID": clientAdd,
                    "OPENED": "Y",
                    "ASSIGNED_BY_ID": this.selectManager,
                    "OPPORTUNITY": this.dealSum.replace(/\s+/g, ''),
                    "CATEGORY_ID": 5,
                    "SOURCE_ID": 4,
                    'UF_CRM_1652460536348': this.projectSelect,
                    "SOURCE_DESCRIPTION": 'Регистрация клиента через форму брокера.'
                  },
              params: {"REGISTER_SONET_EVENT": "Y"}
            })
            await axios.post(this.url + 'im.notify.personal.add', {
              USER_ID: 1939,
              MESSAGE: `Брокер отправил новую заявку https://engeocentr.bitrix24.ru/crm/deal/details/${dealAdd.data.result}/`
            })

             await axios.post(this.url + 'crm.deal.contact.add', {
              id: dealAdd.data.result,
              fields: {CONTACT_ID: brockerAdd}
            })

            const proccess = await axios.post(this.url + 'bizproc.workflow.start', {
              TEMPLATE_ID: 325,
              DOCUMENT_ID: ['crm', 'CCrmDocumentDeal', dealAdd.data.result],
              PARAMETERS: null
            })
            console.log({proccess})
            this.name = ''
            this.brockerSelect = ''
            this.brockerName = ''
            this.brockerPhone = ''
            this.phone = ''
            this.dealSum = 0
            this.loadingDealAdd = false
            alert('Ваш клиент зарегистрирован!')
          } else {
            alert(`Ваш клиент ${this.name} уже зафиксирован за другим контрагентом. Просим Вас связаться с офисом продаж, чтобы выяснить детали +7 (495) 182 22 22`)
            this.name = ''
            this.brockerSelect = ''
            this.brockerName = ''
            this.brockerPhone = ''
            this.phone = ''
            this.dealSum = 0
            this.loadingDealAdd = false
          }
        }
      } catch (e) {
        console.log(e)
      }
    },
    clearDeals() {
      this.name = ''
      this.brockerSelect = ''
      this.brockerName = ''
      this.brockerPhone = ''
      this.phone = ''
      this.dealSum = 0
      this.loadingDealAdd = false
    },
    filterToInn(item, queryText) {
      try {
        const lengthSearchText = queryText.length
        if (lengthSearchText > 0) {
          if (item.INN == queryText) {
            return item.INN == queryText
          } else {
            return item.TITLE.toLowerCase().indexOf(queryText.toLowerCase()) > -1
          }
        }
      } catch (e) {
        console.log(e)
      }
    },
    async addAgency() {
      try {
        this.validateAgency()
        if (this.validAgency) {
          this.loadingAgensy = true
          //ищем агенство по инн
          const agency = await axios.post(this.url + 'crm.company.list', {filter: {'UF_CRM_1613061825761': this.innAgency}})
          if (agency.data.result.length < 1) {
            //создаем агенство
            const addAgency = await axios.post(this.url + 'crm.company.add',
                {
                  fields: {
                    "TITLE": this.titleAgency,
                    "COMPANY_TYPE": "SUPPLIER",
                    "OPENED": "Y",
                    "ASSIGNED_BY_ID": this.selectManager,
                    "PHONE": [{"VALUE": `+7${this.phoneAgency}`, "VALUE_TYPE": "WORK"}],
                    'UF_CRM_1613061825761': this.innAgency,
                  },
                  params: {"REGISTER_SONET_EVENT": "Y"}
                })
            this.brockers.push({ID: addAgency.data.result, TITLE: this.titleAgency, INN: this.innAgency})
            this.brockerSelect = addAgency.data.result
            // ищем брокера
            let brockerAdd = ''
            const newPhoneBrocker = this.brockerPhone.replace(/[^+\d]/g, '')
            const brocker = await axios.post(this.url + 'crm.duplicate.findbycomm',
                {
                  entity_type: "CONTACT",
                  type: "PHONE",
                  values: [`+7${newPhoneBrocker}`, `8${newPhoneBrocker}`]
                })
            if (brocker.data.result.length < 1) {
              const brockerAddData = await axios.post(this.url + 'crm.contact.add', {
                fields: {
                  "NAME": this.brockerName,
                  "OPENED": "Y",
                  "ASSIGNED_BY_ID": this.selectManager,
                  "TYPE_ID": "SUPPLIER",
                  "PHONE": [{"VALUE": `+7${this.brockerPhone}`, "VALUE_TYPE": "WORK"}],
                  'COMPANY_ID': this.brockerSelect
                },
                params: {"REGISTER_SONET_EVENT": "Y"}
              })
              brockerAdd = brockerAddData.data.result
            } else {
              brockerAdd = brocker.data.result.CONTACT[0]
              await axios.post(this.url + 'crm.contact.update', {
                id: brockerAdd,
                fields:
                    {
                      "COMPANY_ID": this.brockerSelect,
                    },
                params: {"REGISTER_SONET_EVENT": "Y"}
              })
            }
            //console.log({addAgency})
            this.loadingAgensy = false
            this.innAgency = ''
            this.phoneAgency = ''
            this.titleAgency = ''
            this.openBrockerAdd()
          } else {
            alert('Агенство с таким ИНН уже существует!')
            this.innAgency = ''
            this.phoneAgency = ''
            this.titleAgency = ''
            this.loadingAgensy = false
            this.openBrockerAdd()
          }
        }
      } catch (e) {
        console.log(e)
      }
    },
    openBrockerAdd() {
      this.menu = !this.menu
    },
    async getBrochers() {
      try {
        const method = 'crm.company.list'
        let i = 0
        let start = 0
        while (i == 0) {
          const params = {
            order: {'TITLE': 'ASC'},
            filter: {
              COMPANY_TYPE: 'SUPPLIER',
            },
            select: ['ID', 'TITLE', 'UF_CRM_1613061825761'],
            start: start
          }
          const companysData = await axios.post(this.url + method, params)
          for (const company of companysData.data.result) {
            this.brockers.push({ID: company.ID, TITLE: company.TITLE, INN: company.UF_CRM_1613061825761})
          }
          if (companysData.data.next) start = companysData.data.next
          else i = 1
        }
      } catch (e) {
        console.log(e)
      }
    },
    async getSalesManagers() {
      try {
        const salesManagers = []
        const users = await axios.post(this.url + 'user.get', {'UF_DEPARTMENT': '99', ACTIVE: 'true'})
        for (const user of users.data.result) {
          salesManagers.push(user.ID)
        }
        this.selectManager = salesManagers[Math.floor((Math.random() * salesManagers.length))]
      } catch (e) {
        console.log(e)
      }
    }
  },
  mounted() {
    this.getBrochers()
    this.getSalesManagers()
  }
};
</script>
