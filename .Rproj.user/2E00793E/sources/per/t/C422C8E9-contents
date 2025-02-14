library(httr)
library(jsonlite)
library(dplyr)

# Definir a URL base da API
base_url <- "https://transparencia.al.gov.br/licitacao/json-editais/"

# Função para buscar dados por ano
fetch_data_by_year <- function(base_url, year, max_retries = 3) {
  params <- list(
    limit = 100,   
    offset = 0,    
    data_realizacao_dti_ = paste0("01/01/", year),  
    data_realizacao_dtf_ = paste0("31/12/", year)   
  )
  
  all_licitacoes <- data.frame()
  max_iterations <- 1000
  iteration <- 0
  retries <- 0
  
  repeat {
    iteration <- iteration + 1
    
    if (iteration > max_iterations) {
      cat("Limite máximo de iterações atingido. Saindo do loop.\n")
      break
    }
    
    # Fazer requisição HTTP GET com User-Agent atualizado
    response <- GET(base_url, query = params, add_headers(
      `User-Agent` = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36",
      `X-Requested-With` = "XMLHttpRequest"
    ))
    
    # Verifica se a resposta é bem-sucedida
    if (status_code(response) == 200) {
      data <- content(response, as = "text", encoding = "UTF-8")
      json_data <- fromJSON(data, flatten = TRUE)
      
      if (!is.null(json_data$rows) && length(json_data$rows) > 0) {
        licitacoes_df <- as.data.frame(json_data$rows)
        all_licitacoes <- bind_rows(all_licitacoes, licitacoes_df)
        
        params$offset <- params$offset + params$limit
      } else {
        cat("Nenhum registro adicional encontrado. Saindo do loop.\n")
        break
      }
      
    } else if (status_code(response) == 502 && retries < max_retries) {
      # Tentar novamente caso erro 502 ocorra
      retries <- retries + 1
      cat("Erro 502 (Bad Gateway) no ano", year, ". Tentativa", retries, "de", max_retries, "\n")
      Sys.sleep(5) # Aguarda antes de tentar novamente
      
    } else {
      cat("Erro ao acessar a API no ano", year, ". Código de status:", status_code(response), "\n")
      print(content(response, as = "text", encoding = "UTF-8")) # Mostra detalhes do erro
      break
    }
  }
  
  return(all_licitacoes)
}

# Loop para buscar dados de múltiplos anos
all_years_licitacoes <- data.frame()

for (year in 2020:2025) {
  cat("\nBuscando dados para o ano:", year, "\n")
  
  licitacoes_for_year <- fetch_data_by_year(base_url, year)
  
  if (nrow(licitacoes_for_year) > 0) {
    all_years_licitacoes <- bind_rows(all_years_licitacoes, licitacoes_for_year)
    cat("Registros obtidos para", year, ":", nrow(licitacoes_for_year), "\n")
  } else {
    cat("Nenhum registro encontrado para o ano:", year, "\n")
  }
  
  Sys.sleep(2) # Evita bloqueios por excesso de requisições
}

# Exibir as primeiras linhas do dataframe consolidado
print(head(all_years_licitacoes))

# Salvar os dados em um arquivo CSV
write.csv(all_years_licitacoes, "editais_2020_a_2025.csv", row.names = FALSE)

cat("Total de registros obtidos:", nrow(all_years_licitacoes), "\n")
