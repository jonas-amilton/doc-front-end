# Criação de layout e theme default

### Passo 1

Criar pasta config com a seguuinte estrutura de arquivos

src/config/layout/DefaultLayout.tsx

e

src/config/theme/defaultTheme.ts

### Passo 2

Arquivo DefaultLayout.tsx

````
import { Container, Grid } from '@mui/material';
import React from 'react';

// import de componente navbar
import ResponsiveAppBar from '../../components/ResponsiveAppBar';

interface DefaultLayoutProps {
  children: React.ReactNode;
}

export const DefaultLayout: React.FC<DefaultLayoutProps> = ({ children }) => {
  return (
    <Grid container>
      <Grid item xs={12}>
        <ResponsiveAppBar />
      </Grid>

      <Grid item xs={12}>
        <Container sx={{ marginTop: '20px' }}>{children}</Container>
      </Grid>
    </Grid>
  );
};
````

### Passo 3

Arquivo defaultTheme.ts

````
import { createTheme } from "@mui/material";

const defaultTheme = createTheme({
  palette: {
    primary: {
      main: "#000",
    },
    secondary: {
      main: "#fff",
    },
  },
});

export default defaultTheme;

````


### Passo 4

Importar layout e theme default em App.tsx

````
import { BrowserRouter } from "react-router-dom";
import { CssBaseline } from "@mui/material";
import { Routers } from "./Routers/Routers";
import { DefaultLayout } from "./config/layout/DefaultLayout";


function App() {
  return (
         
         // store e persistor podem ser importados aqui também conforme necessário

        <BrowserRouter>
         <CssBaseline />
          <DefaultLayout>
            <Routers />
          </DefaultLayout>
        </BrowserRouter>

  );
}

export default App;

````