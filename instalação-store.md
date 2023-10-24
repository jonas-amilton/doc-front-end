# Tutorial de instalação do Redux

### Passo 1

``npm install @reduxjs/toolkit``

### Passo 2

Criar pasta store

src/store

### Passo 3

Criar arquivo index.ts

src/store/index.ts

````
import { configureStore } from '@reduxjs/toolkit';
import { persistStore, persistReducer, FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER } from 'redux-persist';
import storage from 'redux-persist/lib/storage';

import rootReducer from './modules/rootReducer';

const persistConfig = {
  key: 'entity-adapter-reactjs',
  storage,
};

const persistedReducer = persistReducer(persistConfig, rootReducer);

export const store = configureStore({
  reducer: persistedReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    }),
});

export const persistor = persistStore(store);
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
````

### Passo 3

Criar arquivo hooks.ts

src/store/hooks.ts

````
import { useDispatch, useSelector } from 'react-redux';
import type { TypedUseSelectorHook } from 'react-redux';
import type { RootState, AppDispatch } from './index';

// Use throughout your app instead of plain `useDispatch` and `useSelector`
export const useAppDispatch: () => AppDispatch = useDispatch;
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
````

### Passo 4

Criar Slice, no exemplo é criado peopleSlice.ts com propriedades da API de Star Wars

src/store/peopleSlice.ts

````
import {
  createEntityAdapter,
  createSlice,
  createAsyncThunk,
} from "@reduxjs/toolkit";
import { RootState } from "../index";

// Não esquecer de criar a função doGet para consumir a url da api
import { doGet } from "../../api";

// Define o tipo de objeto que será armazenado no estado
export interface PersonType {
  name: string;
  birth_year: string;
  eye_color: string;
  gender: string;
  hair_color: string;
  height: string;
  mass: string;
  skin_color: string;
  homeworld: string;
  films: any;
  species: any;
  starships: any;
  vehicles: any;
  url: string;
  created: string;
  edited: string;
}

// Cria um adaptador que simplifica a manipulação de objetos do tipo PersonType
const adapter = createEntityAdapter<PersonType>({
  selectId: (item) => item.name, // Define como identificar objetos pelo nome
});

// Exporta seletores gerados automaticamente pelo adaptador
export const { selectAll, selectById } = adapter.getSelectors(
  (state: RootState) => state.people
);

// Cria uma função assíncrona que buscará pessoas da API
export const getAllPeoples = createAsyncThunk(
  "people/getAll",
  async (page: number) => {
    let response = await doGet(`/people/?page=${page}`);

    return response.results as PersonType[];
  }
);

// Define um slice do Redux para pessoas, que inclui reducers vazios
const peopleSlice = createSlice({
  name: "people",
  initialState: adapter.getInitialState(),
  reducers: {}, // Reducers podem ser adicionados aqui se necessário
  extraReducers: (builder) => {
    // Quando a ação 'getAllPeoples' for concluída com êxito, atualiza o estado
    builder.addCase(getAllPeoples.fulfilled, (state, action) => {
      console.log(action);
      // Mapeia os resultados e adiciona uma chave 'label' a cada objeto
      const resultadoFinal = action.payload.map((item) => ({
        ...item,
        label: item.name,
      }));
      // Define o estado com os resultados mapeados
      adapter.setAll(state, resultadoFinal);
    });
  },
});

export default peopleSlice.reducer;
````


### Passo 5

Criar pasta modules e arquivo rootReducer.ts

src/store/modules/rootReducer.ts

````
import { combineReducers } from "@reduxjs/toolkit";
import peopleSlice from "./peopleSlice";

export default combineReducers({
  people: peopleSlice,
  // aqui serão passados os slices criados
});
````

### Passo 6 

Importar a store e persistor no App.tsx

Exemplo:

````
import { Provider } from "react-redux";
import { PersistGate } from "redux-persist/integration/react";

import { persistor, store } from "./store";

function App() {
  return (
    <Provider store={store}>
      <PersistGate persistor={persistor}>
      
        // resto do código a ser importado, por exemplo routers e pages...

      </PersistGate>
    </Provider>
  );
}

export default App;
````