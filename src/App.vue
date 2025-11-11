<template>
  <div id="app">
    <!-- NAVBAR -->
    <nav class="navbar">
      <div class="nav-container">
        <h1 class="nav-title">Convertidor de Divisas</h1>
        <div class="nav-info">
          <span>Tasas en tiempo real</span>
        </div>
      </div>
    </nav>

    <!-- CONTENIDO PRINCIPAL -->
    <main class="main-content">
      <div class="converter-box">
        <h2 class="box-title">Convertir Moneda</h2>

        <!-- Monto a convertir -->
        <div class="input-group">
          <label>Cantidad:</label>
          <input
            v-model.number="amount"
            type="number"
            placeholder="Ingresa la cantidad"
            min="0"
            @keyup.enter="convertCurrency"
          />
        </div>

        <!-- Moneda origen -->
        <div class="input-group">
          <label>De:</label>
          <select v-model="fromCurrency" @change="convertCurrency">
            <option value="USD">USD - Dólar Estadounidense</option>
            <option value="EUR">EUR - Euro</option>
            <option value="ARS">ARS - Peso Argentino</option>
            <option value="GBP">GBP - Libra Esterlina</option>
            <option value="BRL">BRL - Real Brasileño</option>
            <option value="MXN">MXN - Peso Mexicano</option>
            <option value="CLP">CLP - Peso Chileno</option>
            <option value="JPY">JPY - Yen Japonés</option>
          </select>
        </div>

        <!-- Botón de intercambio -->
        <div class="swap-container">
          <button @click="swapCurrencies" class="swap-btn" title="Intercambiar monedas">⇅</button>
        </div>

        <!-- Moneda destino -->
        <div class="input-group">
          <label>A:</label>
          <select v-model="toCurrency" @change="convertCurrency">
            <option value="USD">USD - Dólar Estadounidense</option>
            <option value="EUR">EUR - Euro</option>
            <option value="ARS">ARS - Peso Argentino</option>
            <option value="GBP">GBP - Libra Esterlina</option>
            <option value="BRL">BRL - Real Brasileño</option>
            <option value="MXN">MXN - Peso Mexicano</option>
            <option value="CLP">CLP - Peso Chileno</option>
            <option value="JPY">JPY - Yen Japonés</option>
          </select>
        </div>

        <!-- Botón convertir -->
        <button @click="convertCurrency" class="convert-btn" :disabled="loading">
          <span v-if="!loading">Convertir</span>
          <span v-else>⏳ Convirtiendo...</span>
        </button>

        <!-- Resultado -->
        <transition name="fade">
          <div v-if="result !== null && !error" class="result">
            <div class="result-icon">💰</div>
            <p class="result-amount">{{ formatNumber(amount) }} {{ fromCurrency }}</p>
            <div class="result-equals">=</div>
            <p class="result-converted">{{ formatNumber(result) }} {{ toCurrency }}</p>
            <p class="exchange-rate">
              Tasa: 1 {{ fromCurrency }} = {{ exchangeRate.toFixed(4) }} {{ toCurrency }}
            </p>
            <p class="last-update">Última actualización: {{ currentTime }}</p>
          </div>
        </transition>

        <!-- Mensaje de error -->
        <transition name="fade">
          <div v-if="error" class="error">⚠️ {{ error }}</div>
        </transition>
      </div>

      <!-- Información adicional -->
      <div class="info-box">
        <p><strong>Tip:</strong> Presiona Enter para convertir rápidamente</p>
        <p>Las tasas se actualizan automáticamente</p>
      </div>
    </main>

    <!-- FOOTER -->
    <footer class="footer">
      <p>Desarrollado con Vue.js | Tasas proporcionadas por ExchangeRate-API</p>
    </footer>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      amount: 100,
      fromCurrency: 'USD',
      toCurrency: 'ARS',
      result: null,
      exchangeRate: 0,
      loading: false,
      error: null,
      currentTime: '',
    }
  },
  methods: {
    async convertCurrency() {
      // Valida que el monto sea válido
      if (!this.amount || this.amount <= 0) {
        this.error = 'Por favor ingresa una cantidad válida mayor a 0'
        this.result = null
        return
      }

      // Si las monedas son iguales
      if (this.fromCurrency === this.toCurrency) {
        this.result = this.amount
        this.exchangeRate = 1
        this.error = null
        this.updateTime()
        return
      }

      try {
        this.loading = true
        this.error = null

        // Llamada a la API de tasas de cambio
        const response = await fetch(
          `https://api.exchangerate-api.com/v4/latest/${this.fromCurrency}`,
        )

        if (!response.ok) {
          throw new Error('Error al obtener las tasas de cambio')
        }

        const data = await response.json()
        this.exchangeRate = data.rates[this.toCurrency]
        this.result = this.amount * this.exchangeRate
        this.updateTime()
      } catch (err) {
        this.error = 'Error al conectar con el servicio. Verifica tu conexión.'
        this.result = null
        console.error(err)
      } finally {
        this.loading = false
      }
    },

    // Intercambiar monedas
    swapCurrencies() {
      const temp = this.fromCurrency
      this.fromCurrency = this.toCurrency
      this.toCurrency = temp
      if (this.result !== null) {
        this.convertCurrency()
      }
    },

    // Formatear números con separadores de miles
    formatNumber(num) {
      return new Intl.NumberFormat('es-AR', {
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
      }).format(num)
    },

    // Actualizar hora
    updateTime() {
      const now = new Date()
      this.currentTime = now.toLocaleTimeString('es-AR')
    },
  },

  // Convertir automáticamente al cargar
  mounted() {
    this.convertCurrency()
  },
}
</script>

<style scoped>
/* IMPORTAR IMAGEN DE FONDO */
/* Asegúrate de que la imagen esté en src/assets/ */
#app {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;

  /* FONDO CON IMAGEN */
  background-image: url('./assets/fondo1.jpg');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  background-repeat: no-repeat;

  /* Overlay oscuro para mejorar legibilidad */
  position: relative;
}

#app::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 0;
}

/* NAVBAR */
.navbar {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  box-shadow: 0 2px 20px rgba(0, 0, 0, 0.1);
  padding: 20px 0;
  position: sticky;
  top: 0;
  z-index: 100;
}

.nav-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.nav-title {
  margin: 0;
  font-size: 1.8em;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.nav-info {
  color: #666;
  font-size: 0.9em;
  font-weight: 500;
}

/* CONTENIDO PRINCIPAL */
.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px 20px;
  position: relative;
  z-index: 1;
}

.converter-box {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  width: 100%;
  max-width: 500px;
  margin-bottom: 20px;
}

.box-title {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
  font-size: 1.5em;
}

.input-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-weight: 600;
  font-size: 14px;
}

input,
select {
  width: 100%;
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 16px;
  transition: all 0.3s;
  box-sizing: border-box;
  background: white;
}

input:focus,
select:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

/* BOTÓN DE INTERCAMBIO */
.swap-container {
  display: flex;
  justify-content: center;
  margin: 10px 0;
}

.swap-btn {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  border: 2px solid #667eea;
  background: white;
  color: #667eea;
  font-size: 24px;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  align-items: center;
  justify-content: center;
}

.swap-btn:hover {
  background: #667eea;
  color: white;
  transform: rotate(180deg);
}

/* BOTÓN CONVERTIR */
.convert-btn {
  width: 100%;
  padding: 15px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 18px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
  margin-top: 10px;
}

.convert-btn:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
}

.convert-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

/* RESULTADO */
.result {
  margin-top: 30px;
  padding: 25px;
  background: linear-gradient(135deg, #667eea15 0%, #764ba215 100%);
  border-radius: 15px;
  text-align: center;
  border: 2px solid #667eea30;
}

.result-icon {
  font-size: 3em;
  margin-bottom: 10px;
}

.result-amount,
.result-converted {
  font-size: 1.4em;
  color: #333;
  margin: 10px 0;
  font-weight: 600;
}

.result-equals {
  font-size: 2em;
  color: #667eea;
  margin: 10px 0;
}

.exchange-rate {
  color: #666;
  font-size: 0.95em;
  margin-top: 15px;
  padding-top: 15px;
  border-top: 1px solid #ddd;
}

.last-update {
  color: #999;
  font-size: 0.85em;
  margin-top: 8px;
}

/* ERROR */
.error {
  margin-top: 20px;
  padding: 15px;
  background: #fee;
  border: 2px solid #fcc;
  border-radius: 8px;
  color: #c33;
  text-align: center;
  font-weight: 600;
}

/* INFO BOX */
.info-box {
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  padding: 20px;
  border-radius: 15px;
  max-width: 500px;
  width: 100%;
  text-align: center;
}

.info-box p {
  margin: 8px 0;
  color: #555;
  font-size: 0.9em;
}

/* FOOTER */
.footer {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  padding: 20px;
  text-align: center;
  color: #666;
  font-size: 0.9em;
  position: relative;
  z-index: 1;
}

/* ANIMACIONES */
.fade-enter-active,
.fade-leave-active {
  transition:
    opacity 0.5s,
    transform 0.5s;
}

.fade-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}

/* RESPONSIVE */
@media (max-width: 768px) {
  .nav-container {
    flex-direction: column;
    gap: 10px;
  }

  .nav-title {
    font-size: 1.5em;
  }

  .converter-box {
    padding: 25px;
  }
}
</style>
