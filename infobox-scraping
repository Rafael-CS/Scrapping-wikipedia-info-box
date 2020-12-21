import json
import requests
from bs4 import BeautifulSoup
from unidecode import unidecode

def get_minister_info(minister_name):
  wiki_url = 'https://pt.wikipedia.org/wiki/'
  minister_name = minister_name.replace(" ", "_")
  wiki_url = wiki_url + minister_name

  page = requests.get(wiki_url)
  soup = BeautifulSoup(page.text, 'html.parser')
  table_content = soup.find("table").findAll("tr")

  final_dict = dict()
  final_dict["secoes"] = []
  secao_dict = dict()
  secao_dict["informacoes"] = []

  for content in table_content:
    tds = content.findAll("td")
    if(len(tds) == 1):
      # Se for a imagem principal
      if(content.find("img")):
        final_dict["foto"] = content.find("img")['src']

      # Se for o título
      elif(content.find("a")):
        if("titulo" in final_dict):
          if(len(secao_dict.keys()) > 0):
             final_dict["secoes"].append(secao_dict)
          secao_dict = dict()
          secao_dict["informacoes"] = []
          secao_dict["titulo"] = unidecode(str(content.find("a").contents[0]))
        else:
          final_dict["titulo"] = unidecode(str(content.find("a").contents[0]))
      else:
        if("titulo" in final_dict):
          if(len(secao_dict.keys()) > 0):
              final_dict["secoes"].append(secao_dict)
          secao_dict = dict()
          secao_dict["informacoes"] = []
          secao_dict["titulo"] = minister_name
        else:
          final_dict["titulo"] = minister_name
    elif(len(tds) > 1):
      info_dict = dict()

      # Se for um subtítulo
      if(tds[0].find("span")):
        info_dict["titulo"] = unidecode(str(tds[0].find("span").contents[0]).replace('\n', ''))
      elif(tds[0].find("a")):
        info_dict["titulo"] = unidecode(str(tds[0].find("a").contents[0]).replace('\n', ''))
      else:
        info_dict["titulo"] = unidecode(str(tds[0].contents[0]).replace('\n', ''))
      # Se for uma informação
      if(tds[1].find("img")):
        info_dict["informacao"] = unidecode(str(tds[1].find("img")['src']).replace('\n', ''))
      elif(tds[1].find("a")):
        info_dict["informacao"] = unidecode(str(tds[1].find("a").contents[0]).replace('\n', ''))
      else:
        info_dict["informacao"] = unidecode(str(tds[1].contents[0]).replace('\n', ''))
  
      secao_dict["informacoes"].append(info_dict)

  if(len(secao_dict.keys()) > 0 and len(secao_dict["informacoes"]) > 0):
    final_dict["secoes"].append(secao_dict)
  
  return final_dict

def main(file_name):
    # Read minister names on input file
    ministers_names = set()
    ministers_info = {}

    f = open(file_name, "r")
    if f.mode == 'r':
      file_content = f.read()
      file_content = file_content.splitlines()
      for minister_name in file_content:
        ministers_names.add(minister_name)

    # Loop to find each minister info
    for minister_name in ministers_names:
      found_info = get_minister_info(minister_name)
      ministers_info[minister_name] = found_info
      print(minister_name)

    with open('data.json', 'w') as fp:
      json.dump({"informacao_ministros" : ministers_info }, fp)
 
main("name.txt") 
