<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-4">
        <MenuList 
          :data="data" 
          :cart="cart"
          @add-to-cart="addToCart"
        />
      </div>
      <div class="col-md-8">
        <OperationList 
          :cart="cart" 
          :sum="sum"
          v-model:description="description"
          @remove-from-cart="removeFromCart"
          @update-cart="updateCart"
          @create-order="createOrder"
        />
      </div>
    </div>
    <hr />
    <div class="row">
        <DashboardList
          :orders="orders"
          :preparedOrder="preparedOrder"
          :doneOrder="doneOrder"
          :topDrinks="topDrinks"
          :totalValue="totalValue"
          @change-tab="changeTab"
        />
        <OrderList 
          :orders="orders"
          :filterOrder="filterOrder"
          :fullClass="fullClass"
          :halfClass="halfClass"
          :successClass="successClass"
          :pendingClass="pendingClass"
          @change-status="changeStatus"
          @cancel-order="cancelOrder"
        />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import MenuList from '@/components/MenuList.vue';
import OperationList from '@/components/OperationList.vue';
import DashboardList from '@/components/DashboardList.vue';
import OrderList from '@/components/OrderList.vue';

const data = [
  {
    id: 1,
    name: '珍珠奶茶',
    description: '香濃奶茶搭配QQ珍珠',
    price: 50,
  },
  {
    id: 2,
    name: '冬瓜檸檬',
    description: '清新冬瓜配上新鮮檸檬',
    price: 45,
  },
  {
    id: 3,
    name: '翡翠檸檬',
    description: '綠茶與檸檬的完美結合',
    price: 55,
  },
  {
    id: 4,
    name: '四季春茶',
    description: '香醇四季春茶，回甘無比',
    price: 45,
  },
  {
    id: 5,
    name: '阿薩姆奶茶',
    description: '阿薩姆紅茶搭配香醇鮮奶',
    price: 50,
  },
  {
    id: 6,
    name: '檸檬冰茶',
    description: '檸檬與冰茶的清新組合',
    price: 45,
  },
  {
    id: 7,
    name: '芒果綠茶',
    description: '芒果與綠茶的獨特風味',
    price: 55,
  },
  {
    id: 8,
    name: '抹茶拿鐵',
    description: '抹茶與鮮奶的絕配',
    price: 60,
  },
];

const pendingClass = ref('btn btn-sm btn-primary');
const successClass = ref('btn btn-sm btn-success');
const halfClass = ref('col-6');
const fullClass = ref('col-12');
const drinks = ref(data);
const cart = ref([]);
const description = ref('');
const selectedTab = ref('');
const orders = ref([]);
const preparedOrder =  computed(() => {
  return orders.value.filter((cartItem) => cartItem.status === false) 
});
const doneOrder =  computed(() => {
  return orders.value.filter((cartItem) => cartItem.status === true) 
});
const changeTab = (value) => {
  selectedTab.value = value
}
const filterOrder = computed(() => {
  if(selectedTab.value === "" || selectedTab.value === '1'){ // All orders
    return orders.value
  }else if(selectedTab.value === '2'){ // Prepared Order
    return orders.value.filter((cartItem) => cartItem.status === false) 
  }else if(selectedTab.value ==='3'){ // Done Order
    return orders.value.filter((cartItem) => cartItem.status === true) 
  } 
});
const sum = computed(() => {
  return cart.value.reduce((pre, next) => {
    return pre + next.price * next.quantity;
  }, 0);
});
const totalValue = computed(() => {
  return orders.value.filter((cartItem) => cartItem.status === true).map(v=>v.sum).reduce((pre, next) => {
    return pre + next;
  }, 0);
});
const topDrinks = computed(() => {
    let drinkTotals = {};
    orders.value.map((cartItem) => cartItem.cart).flat().forEach((item) => {
      if (drinkTotals[item.name]) {
        drinkTotals[item.name] += item.quantity;
      } else {
        drinkTotals[item.name] = item.quantity;
      }
    });
    
    const sortedDrinks = Object.keys(drinkTotals)
    .map((name) => ({
      name: name,
      totalQuantity: drinkTotals[name]
    }))
    .sort((a, b) => b.totalQuantity - a.totalQuantity);

    console.log("sortedDrinks:",sortedDrinks) // All Rink

  return sortedDrinks.slice(0, 3); // Top 3
});
const addToCart = (drink) => {
  const findIndex = cart.value.findIndex((cartItem) => cartItem.name === drink.name);
  if(findIndex === -1){
      cart.value.push({
      ...drink,
      id: new Date().getTime(),
      quantity: 1,
    });
  }else{
   
    if(cart.value[findIndex].quantity < 10){
      cart.value[findIndex].quantity++
      console.log(drink.name + " in cart, quantity + 1")
    }else{
      console.log(drink.name + " reach limit order 10")
    }
  }
};
const updateCart = (item) => {
  cart.value = cart.value.map((cartItem) => {
    if (cartItem.id === item.id) {
      cartItem.quantity = parseInt(item.quantity);
    }
    return cartItem;
  });
};
const removeFromCart = (id) => {
  cart.value = cart.value.filter((cartItem) => cartItem.id !== id);
};
const createOrder = () => {
  const d = new Date()
  // Tue Apr 28 2020 13:14:20 GMT+0600 (孟加拉標準時間)

  d.setHours(d.getHours() + 8)
  // Tue Apr 28 2020 21:14:20 GMT+0600 (孟加拉標準時間)

  let dt = d.toJSON().slice(0, 19).replace('T', ' ')
  // 2020-04-28T15:14:20
  
  orders.value.push({
    id: new Date().getTime(),
    cart: cart.value,
    description: description.value,
    sum: sum.value,
    status: false,
    orderTime: dt
  });
  cart.value = [];
  description.value = '';
};
const changeStatus = (order) => {
  order.status =!order.status;
}
const cancelOrder = (id) => {
  orders.value = orders.value.filter((cartItem) => cartItem.id !== id);
};
</script>