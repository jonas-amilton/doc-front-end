# Tutorial de consumo de API

### Passo 1

``npm i axios && npm i -D typescript``

### Passo 2

Criar arquivo .env

``REACT_APP_API_URL=https://url/api``

### Passo 3

Criar pasta api e arquivo index.ts

src/api/index.ts

````
import axios from "axios";

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
});

async function doGet(url: string) {
  try {
    const response = await api.get(url);
    return response.data;
  } catch (error) {
    console.log(error);
    return { success: false, data: { msg: "Server error get." } };
  }
}

export { doGet };
````