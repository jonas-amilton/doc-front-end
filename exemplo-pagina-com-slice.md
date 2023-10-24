# Exemplo de página utilizando slice

````
import React, { useEffect, useState } from "react";
import {
  Grid,
  Typography,
  Card,
  CardContent,
  TextField,
  Button,
} from "@mui/material";
import TitlePage from "../../components/TitlePage";
import { useAppDispatch, useAppSelector } from "../../store/hooks";
import { getAllPeoples, selectAll } from "../../store/modules/peopleSlice";

export const Characters: React.FC = () => {
  // Inicializa os hooks do Redux para dispatch e seleção
  const dispatch = useAppDispatch();
  const peopleRedux = useAppSelector(selectAll);

  // Estado para o texto de pesquisa e página atual
  const [searchText, setSearchText] = useState("");
  let [currentPage, setCurrentPage] = useState(1);

  // Função para lidar com a página anterior
  const handlePreviousPage = () => {
    if (currentPage > 1) {
      setCurrentPage(currentPage - 1);
      dispatch(getAllPeoples(currentPage - 1));
    } else if (currentPage > 9) {
      dispatch(getAllPeoples((currentPage = 1)));
    }
  };

  // Função para lidar com a próxima página
  const handleNextPage = () => {
    setCurrentPage(currentPage + 1);
    if (currentPage < 9) {
      dispatch(getAllPeoples(currentPage + 1));
    } else {
      dispatch(getAllPeoples(currentPage - 8));
    }
  };

  // Busca dados quando o componente é montado
  useEffect(() => {
    dispatch(getAllPeoples(1));
  }, []);

  // Filtra as pessoas com base no texto de pesquisa
  const filteredPeople = peopleRedux.filter(
    (item) =>
      item.name.toLowerCase().includes(searchText.toLowerCase()) ||
      item.gender.toLowerCase().includes(searchText.toLowerCase()) ||
      item.hair_color.toLowerCase().includes(searchText.toLowerCase()) ||
      item.skin_color.toLowerCase().includes(searchText.toLowerCase()) ||
      item.eye_color.toLowerCase().includes(searchText.toLowerCase())
  );

  return (
    <Grid container>
      <TitlePage title="Personagens de Star Wars" />

      {/* Campo de entrada de pesquisa */}
      <Grid container spacing={2}>
        <Grid item xs={12}>
          <TextField
            label="Buscar"
            fullWidth
            margin="normal"
            value={searchText}
            onChange={(e) => setSearchText(e.target.value)}
          />
        </Grid>
      </Grid>

      {filteredPeople.length > 0 ? (
        // Exibe os cards das pessoas filtradas
        filteredPeople.map((item) => (
          <Grid item xs={12} sm={6} md={4} key={item.name}>
            <Card
              style={{
                margin: ".4em",
                backgroundColor: "black",
                color: "white",
              }}
            >
              <CardContent>
                <Typography variant="h5">{item.name}</Typography>
                <Typography variant="body1">
                  Ano de nascimento: {item.birth_year}
                </Typography>
                <Typography variant="body1">
                  Cor dos olhos: {item.eye_color}
                </Typography>
                <Typography variant="body1">Gênero: {item.gender}</Typography>
                <Typography variant="body1">
                  Cor do cabelo: {item.hair_color}
                </Typography>
                <Typography variant="body1">Altura: {item.height}</Typography>
                <Typography variant="body1">Peso: {item.mass}</Typography>
                <Typography variant="body1">
                  Cor da pele: {item.skin_color}
                </Typography>
              </CardContent>
            </Card>
          </Grid>
        ))
      ) : (
        // Exibe uma mensagem se nenhum personagem for encontrado
        <Typography variant="h6">Nenhum personagem encontrado.</Typography>
      )}

      {/* Botões de paginação */}
      <Grid
        style={{ marginBottom: "2em" }}
        justifyContent={"center"}
        container
        spacing={2}
      >
        <Grid item xs={6}>
          <Button
            fullWidth
            style={{ backgroundColor: "#000", color: "#fff" }}
            onClick={handlePreviousPage}
            variant="contained"
          >
            Página anterior
          </Button>
        </Grid>
        <Grid item xs={6}>
          <Button
            fullWidth
            style={{ backgroundColor: "#000", color: "#fff" }}
            onClick={handleNextPage}
            variant="contained"
          >
            Próxima página
          </Button>
        </Grid>
      </Grid>
    </Grid>
  );
};
````