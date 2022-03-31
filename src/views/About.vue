<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button id="your-button">Paypal</button>
    <div v-if="!paidFor">
      <div v-for="item in products" :key="item.id">
        <h2>{{ item.name }}</h2>
        <img :src="`${item.thumbnail}`" width="350" />
        <span>{{ item.price }}</span>
      </div>
      <div ref="paypal"></div>
    </div>
  </div>
</template>
<script>
import { loadScript } from "@paypal/paypal-js";
export default {
  data: () => {
    return {
      result: null,
      loaded: false,
      paidFor: false,
      products: [
        {
          id: "01",
          name: "Estufa",
          price: "$15,400.00",
          thumbnail: "https://picsum.photos/400",
        },
      ],
      description: "",
      total: 0,
    };
  },
  mounted() {
    this.callApi();
    this.setLoad();
  },
  methods: {
    callApi() {
      let paypal;
      try {
        paypal = loadScript({
          "client-id":
            "AR3n86cIAYeUQc_VQ-Eh011DbLKIt2hB9oo9EHCJ1JsRjId79PEAlzd_gZe1HQFK1UZY14lIlvGyTQq5",
        });
      } catch (error) {
        console.log("failed to load  the Paypal JS SDK", error);
      }
      if (paypal) {
        try {
          paypal.Buttons().render("#your-button");
        } catch (error) {
          console.log("failed to render the PayPal Buttons", error);
        }
      }
    },
    setLoad: function () {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.products.name,
                  amount: {
                    currency_code: "USD",
                    value: this.products.price,
                  },
                },
              ],
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.data;
            this.paidFor = true;
            console.log(order);
          },
          OnError: (err) => {
            console.log(err);
          },
        })
        .render(this.$refs.paypal);
    },
  },
};
</script>