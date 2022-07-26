<template>
  <div id="confirm-order">
    <SfHeading
      title="Order details"
      :level="3"
      class="sf-heading--left sf-heading--no-underline title"
    />
    <SfTable class="sf-table--bordered table">
      <SfTableHeading class="table__row">
        <SfTableHeader class="table__header table__image">Item</SfTableHeader>
        <SfTableHeader
          v-for="tableHeader in tableHeaders"
          :key="tableHeader"
          class="table__header"
          :class="{ table__description: tableHeader === 'Description' }"
          >{{ tableHeader }}
        </SfTableHeader>
      </SfTableHeading>
      <SfTableRow
        v-for="(product, index) in products"
        :key="index"
        class="table__row"
      >
        <SfTableData class="table__image">
          <SfImage :src="product.image" :alt="product.title" />
        </SfTableData>
        <SfTableData class="table__description">
          <div class="product-title">{{ product.title }}</div>
        </SfTableData>
        <SfTableData class="table__data">{{ product.qty }}</SfTableData>
        <SfTableData class="table__data">
          <SfProperty
            v-for="(property, key) in product.configuration"
            :key="'property' + key"
            :name="property.name"
            :value="property.value"
          />
        </SfTableData>
        <SfTableData class="table__data">
          <SfPrice
            :regular="product.price.regular"
            :special="product.price.special"
            class="product-price"
          />
        </SfTableData>
      </SfTableRow>
    </SfTable>
    <div class="summary smartphone-only">
      <div class="summary__content">
        <SfProperty
          name="Subtotal"
          :value="subtotal"
          class="sf-property--full-width property"
        />
        <SfProperty
          name="Shipping"
          :value="shippingMethod ? '$' + shippingMethod.cost : '$0'"
          class="sf-property--full-width property"
        />
        <SfDivider class="divider" />
        <SfProperty
          name="Total price"
          :value="total"
          class="sf-property--full-width property"
        />
        <SfCheckbox v-model="terms" name="terms" class="summary__terms">
          <template #label>
            <div class="sf-checkbox__label">
              I agree to <a href="#">Terms and conditions</a>
            </div>
          </template>
        </SfCheckbox>
      </div>
    </div>
    <div class="totals desktop-only">
      <SfProperty
        name="Subtotal"
        :value="subtotal"
        class="sf-property--full-width property property__subtotal"
      >
      </SfProperty>
      <SfProperty
        name="Shipping"
        :value="shippingMethod ? '$' + shippingMethod.cost : '$0'"
        class="sf-property--full-width property"
      >
      </SfProperty>
      <SfDivider class="divider" />
      <SfProperty
        name="Total price"
        :value="total"
        class="sf-property--full-width sf-property--large property__total"
      >
      </SfProperty>
      <div class="form">
        <SfHeading
          title="Payment methods"
          :level="3"
          class="sf-heading--left sf-heading--no-underline title"
        />
        <div class="payment-methods">
          <SfSelect
            v-model="paymentMethodId"
            label="Available Payment methods"
            :required="true"
            :valid="true"
            error-message="This option should be selected"
            placeholder="Please select payment method"
            style="max-width: 30rem; margin: 10px"
          >
            <SfSelectOption
              v-for="option in paymentOptions"
              :key="option.id"
              :value="option.id"
              :label="option.name"
            >
              <SfProductOption :label="option.name"></SfProductOption>
            </SfSelectOption>
          </SfSelect>
        </div>
        <div v-if="paymentMethodId" class="payment-methods">
          <SfRadio
            v-for="item in paymentMethods"
            :key="item.value"
            v-model="paymentMethod"
            :label="item.label"
            :value="item.value"
            name="paymentMethod"
            :description="item.description"
            class="form__radio payment-method"
          >
            <template #label>
              <div class="sf-radio__label">
                <template
                  v-if="
                    item.value !== 'debit' &&
                    item.value !== 'mastercard' &&
                    item.value !== 'electron'
                  "
                >
                  {{ item.label }}
                </template>
                <template v-else>
                  <SfImage
                    :src="`/assets/storybook/checkout/${item.value}.png`"
                    :alt="item.value"
                    class="payment-image"
                    :lazy="false"
                  />
                </template>
              </div>
            </template>
          </SfRadio>
        </div>
        <transition name="sf-fade">
          <div v-if="isCreditCard" class="credit-card-form">
            <SfInput
              v-model="$v.cardInfo.cardNumber.$model"
              :value="cardInfo.cardNumber"
              name="cardNumber"
              label="Card number"
              class="credit-card-form__input"
              :valid="checkFormValidation($v.cardInfo.cardNumber)"
              :error-message="'Card number is required'"
            />
            <SfInput
              v-model="$v.cardInfo.cardHolder.$model"
              :value="cardInfo.cardHolder"
              label="Card holder"
              name="cardHolder"
              class="credit-card-form__input"
              :valid="checkFormValidation($v.cardInfo.cardHolder)"
              :error-message="'Card holder name is required'"
            />
            <div class="credit-card-form__group">
              <span
                class="credit-card-form__label credit-card-form__label--small credit-card-form__label--required"
                >Expiry date:</span
              >
              <div class="credit-card-form__element">
                <div class="credit-card-form__item">
                  <SfSelect
                    v-model="cardInfo.cardMonth"
                    :value="cardInfo.cardMonth"
                    label="Month"
                    class="credit-card-form__input credit-card-form__input--with-spacer form__select sf-select--underlined"
                  >
                    <SfSelectOption
                      v-for="(monthOption, key) in months"
                      :key="monthOption"
                      :value="key + 1"
                    >
                      {{ monthOption }}
                    </SfSelectOption>
                  </SfSelect>
                  <span
                    v-if="!checkFormValidation($v.cardInfo.cardMonth)"
                    style="margin-top: 50px; color: red; font-size: 14px"
                    >Card month is required</span
                  >
                </div>
                <div class="credit-card-form__item">
                  <SfSelect
                    v-model="cardInfo.cardYear"
                    :value="cardInfo.cardYear"
                    label="Year"
                    class="credit-card-form__input form__select sf-select--underlined"
                  >
                    <SfSelectOption
                      v-for="yearOption in years"
                      :key="yearOption"
                      :value="yearOption"
                    >
                      {{ yearOption }}
                    </SfSelectOption>
                  </SfSelect>
                  <span
                    v-if="!checkFormValidation($v.cardInfo.cardYear)"
                    style="margin-top: 50px; color: red; font-size: 14px"
                    >Card year is required</span
                  >
                </div>
              </div>
            </div>
            <div class="credit-card-form__group">
              <SfInput
                v-model="$v.cardInfo.cardCVC.$model"
                :value="cardInfo.cardCVC"
                type="number"
                label="Code CVC"
                name="cardCVC"
                class="credit-card-form__input credit-card-form__input--small credit-card-form__input--with-spacer"
                :valid="checkFormValidation($v.cardInfo.cardCVC)"
                :error-message="'Card CVC is required'"
              />
              <SfButton class="sf-button--text credit-card-form__button"
                >Where can I find CVC code</SfButton
              >
            </div>
          </div>
        </transition>
      </div>
      <SfCheckbox v-model="terms" name="terms" class="totals__terms">
        <template #label>
          <div class="sf-checkbox__label">
            I agree to <SfLink href="#">Terms and conditions</SfLink>
          </div>
        </template>
      </SfCheckbox>
    </div>
  </div>
</template>
<script>
import {
  SfHeading,
  SfTable,
  SfCheckbox,
  SfDivider,
  SfImage,
  SfPrice,
  SfProperty,
  SfLink,
  SfButton,
  SfSelect,
  SfRadio,
  SfInput,
  SfProductOption
} from '@storefront-ui/vue';
import { mapGetters } from 'vuex';
import { required } from 'vuelidate/lib/validators';
import { checkFormValidation } from '~/utils/validation';
export default {
  name: 'ReviewOrder',
  components: {
    SfHeading,
    SfTable,
    SfCheckbox,
    SfDivider,
    SfImage,
    SfPrice,
    SfProperty,
    SfLink,
    SfButton,
    SfSelect,
    SfRadio,
    SfInput,
    SfProductOption
  },
  props: {
    characteristics: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      terms: false,
      promoCode: '',
      tableHeaders: ['Description', 'Quantity', 'Configuration', 'Amount'],
      paymentMethods: [
        {
          label: 'Visa Debit',
          value: 'debit'
        },
        {
          label: 'MasterCard',
          value: 'mastercard'
        },
        {
          label: 'Visa Electron',
          value: 'electron'
        }
      ],
      paymentMethod: '',
      cardInfo: {
        cardNumber: '',
        cardHolder: '',
        cardMonth: '',
        cardYear: '',
        cardCVC: ''
      },
      months: [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
      ],
      years: [2021, 2022, 2023, 2024, 2025],
      paymentMethodId: null
    };
  },
  validations: {
    cardInfo: {
      cardNumber: {
        required
      },
      cardHolder: {
        required
      },
      cardMonth: {
        required
      },
      cardYear: {
        required
      },
      cardCVC: {
        required
      }
    }
  },
  computed: {
    ...mapGetters('carts', ['products']),
    ...mapGetters('checkout', ['shippingMethod', 'paymentOptions']),
    isCreditCard() {
      return ['debit', 'mastercard', 'electron'].includes(this.paymentMethod);
    },
    subtotal() {
      const subtotal = this.products.reduce((previous, current) => {
        const qty = current.qty;
        const price = current.price.special
          ? current.price.special
          : current.price.regular;
        const total = qty * parseFloat(price);
        return previous + total;
      }, 0);
      return '$' + subtotal.toFixed(2);
    },
    total() {
      const subtotal = parseFloat(this.subtotal.replace('$', ''));
      const shipping = parseFloat(
        this.shippingMethod ? this.shippingMethod.cost : 0
      );
      const total = subtotal + shipping;
      return '$' + total.toFixed(2);
    }
  },
  methods: {
    checkFormValidation,
    runAction() {
      if (!this.terms || !this.paymentMethodId || this.$v.$invalid)
        return false;
      const data = {
        payment: {
          instrument: {
            type: 'card',
            number: this.cardInfo.cardNumber,
            cardholder_name: this.cardInfo.cardHolder,
            expiry_month: this.cardInfo.cardMonth,
            expiry_year: this.cardInfo.cardYear,
            verification_value: this.cardInfo.cardCVC
          },
          payment_method_id: this.paymentMethodId
        }
      };
      this.$store.dispatch('checkout/processPayment', data);
      return true;
    }
  }
};
</script>
<style
  src="~/assets/sass/components/checkout/confirmorder.scss"
  lang="scss"
  scoped
></style>
