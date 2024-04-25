<template>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <h3 class="text-light">Carrito de compras</h3>
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                    <h3 class="text-light">Seleccione un producto</h3>
                </li>
            </ul>
        </div>
    </nav>
    <div class="container">
        <div class="row m-3">
            <div class="col-8">
                <h2>Productos de Supermercado</h2>
                <table class="table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Nombre</th>
                            <th>Categoría</th>
                            <th>Precio</th>
                            <th>Stock</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="custom-cursor" v-for="(producto, index) in productos" :key="producto.id"
                            :class="{ 'table-success': selectedRow === index }" @click="mostrarDetalle(producto, index)">
                            <td>{{ producto.id }}</td>
                            <td>{{ producto.nombre }}</td>
                            <td>{{ producto.categoria }}</td>
                            <td>{{ producto.precio }}</td>
                            <td>{{ producto.cantidad }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="col-4">
                <div>
                    <h2>Detalles del Producto</h2>
                    <div class="row" v-if="productoSeleccionado">
                        <div class="col-6">
                            <p class="mt-3 mb-0">ID: </p>
                            <input v-model="productoSeleccionado.id" type="text" readonly>
                            <p class="mt-3 mb-0">Nombre:</p>
                            <input v-model="productoSeleccionado.nombre" type="text" readonly>
                            <p class="mt-3 mb-0">Categoria:</p>
                            <input v-model="productoSeleccionado.categoria" type="text" readonly>
                        </div>
                        <div class="col-6">
                            <p class="mt-3 mb-0">Precio:</p>
                            <input v-model="productoSeleccionado.precio" type="text" readonly>
                            <div style="display: flex; align-items: center">
                                <p class="m-3 ">Cantidad: </p>
                                <input v-model="cantidad" value=0 style="width:50px" type="number" min="1">
                            </div>
                            <div>
                                <button @click="addProducto()" type="button" class="btn btn-primary">
                                    Agregar producto al carrito
                                </button>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </div>
        <div class="row m-3">
            <div class="col-8">
                <h1>Carrito</h1>
                <table class="table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Nombre</th>
                            <th>Categoría</th>
                            <th>Precio</th>
                            <th>Cantidad</th>
                            <th>Total</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr class="custom-cursor" v-for="(product, index) in carrito" v-bind:key="index"
                            :class="{ 'table-danger': deletedRow === index }" @click="eliminar(index)">
                            <td>{{ product.id }}</td>
                            <td>{{ product.nombre }}</td>
                            <td>{{ product.categoria }}</td>
                            <td>{{ product.precio }}</td>
                            <td>{{ product.cantidad }}</td>
                            <td>{{ precios[index] }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <div class="col-4">
                <h1>Resumen</h1>
                <p class="mt-3 mb-0">Cantidad de productos:</p>
                <input v-model="productosTotal" type="text" readonly>
                <p class="mt-3 mb-0">Precio total:</p>
                <input v-model="precioTotal" type="text" readonly>
                <div class="container">
                    <div class="row">
                        <div v-if="buttonEli" class="col-md-8 mx-auto mt-3">
                            <button @click="delProducto()" class="btn btn-danger">Eliminar producto del carrito</button>
                        </div>
                        <div class="col-md-6 mx-auto mt-3">
                            <button @click="finalizarCompra()" class="btn btn-warning">Finalizar compra</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import productos from '../productos.js';

export default {
    data() {
        return {
            deletedRow: null,
            selectedRow: null,
            productos: productos,
            productoSeleccionado: null,
            productoSecundario: null,
            precios: [],
            carrito: [],
            cantidad: 1,
            precioTotal: 0,
            productosTotal: 0,
            buttonEli: null,
        };
    },
    methods: {
        mostrarDetalle(producto, index) {
            this.productoSeleccionado = { ...producto };
            this.selectedRow = index;
        },
        eliminar(index) {
            this.deletedRow = index;
            this.buttonEli = true;
        },
        addProducto() {
            this.productoSeleccionado.cantidad = this.cantidad
            this.carrito.push({ ...this.productoSeleccionado })
            this.precios.push((this.cantidad * this.productoSeleccionado.precio).toFixed(2))
            this.productosTotal = this.carrito.reduce((acc, carrito) => acc + parseInt(carrito.cantidad), 0)
            this.precioTotal = (this.precios.reduce((acc, curr) => acc + parseFloat(curr), 0)).toFixed(2)
            this.productoSeleccionado.id = ""
            this.productoSeleccionado.nombre = ""
            this.productoSeleccionado.categoria = ""
            this.productoSeleccionado.precio = ""
            this.cantidad = 1
            this.productoSeleccionado = null,
            this.selectedRow = null;
        },
        delProducto() {
            this.carrito.splice(this.deletedRow, 1);
            this.precios.splice(this.deletedRow, 1);
            this.productosTotal = this.carrito.reduce((acc, carrito) => acc + parseInt(carrito.cantidad), 0)
            this.precioTotal = (this.precios.reduce((acc, curr) => acc + parseFloat(curr), 0)).toFixed(2)
            this.deletedRow = null
            this.buttonEli = false
        },
        finalizarCompra() {
            alert('Venta realizada exitosamente.\nPrecio total de venta: ' + this.precioTotal);
            this.deletedRow = null
            this.buttonEli = false
            this.productoSeleccionado = null,
            this.selectedRow = null;
            this.carrito = []
            this.precios = []
            this.precioTotal = 0
            this.productosTotal = 0
        }
    },
};
</script>

<style>
input {
    width: 100%;
}

.custom-cursor {
    cursor: pointer;
}
</style>