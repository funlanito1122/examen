import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() {
  runApp(MyApp());
}
List<String> selectedPokemonNames = [];
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
       home: Builder(
        builder: (BuildContext context) {
          return Scaffold(
            appBar: AppBar(
              title: Text('Lista de Pokémon'),
              actions: [
                IconButton(
                  icon: Icon(Icons.list),
                  onPressed: () {
                    _showSelectedPokemonList(context);
                  },
                ),
              ],
            ),
            body: PokemonList(),
          );
        },
      ),
    );
  }
}

void _showSelectedPokemonList(BuildContext context) {
  showDialog(
    context: context,
    builder: (BuildContext context) {
      return AlertDialog(
        title: Text('Pokémon favoritos'),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            if (selectedPokemonNames.isEmpty)
              Text('No se ha seleccionado ningún Pokémon.'),
            for (var name in selectedPokemonNames)
              ListTile(
                title: Text(name),
              ),
          ],
        ),
        actions: [
          TextButton(
            onPressed: () {
              Navigator.of(context).pop(); // Cerrar el diálogo
            },
            child: Text('Cerrar'),
          ),
        ],
      );
    },
  );
}






class PokemonList extends StatefulWidget {
  @override
  _PokemonListState createState() => _PokemonListState();
}

class _PokemonListState extends State<PokemonList> {
  List<Map<String, String>> pokemonData = [];

  @override
  void initState() {
    super.initState();
    fetchPokemonList();
  }

  void fetchPokemonList() async {
    final url = Uri.parse('https://pokeapi.co/api/v2/pokemon?limit=5');
    final response = await http.get(url);
    if (response.statusCode == 200) {
      final data = json.decode(response.body);
      final results = data['results'];
      List<Map<String, String>> pokemonList = [];
      for (var pokemon in results) {
        final name = pokemon['name'];
        final id = results.indexOf(pokemon) + 1;
        final imageUrl = 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/$id.png';
        pokemonList.add({
          'name': name,
          'imageUrl': imageUrl,
          'id': id.toString(),
        });
      }
      setState(() {
        pokemonData = pokemonList;
      });
    } else {
      print('Error en la solicitud: ${response.statusCode}');
    }
  }
  Future<List<String>> fetchPokemonImages(int id) async {
  final url = Uri.parse('https://pokeapi.co/api/v2/pokemon-form/$id/');
  final response = await http.get(url);
  Set<String> additionalImageUrls = Set<String>(); // Usar un conjunto para evitar duplicados

  if (response.statusCode == 200) {
    final data = json.decode(response.body);
    final sprites = data['sprites'];

    for (var key in sprites.keys) {
      if (sprites[key] != null && sprites[key] is String) {
        additionalImageUrls.add(sprites[key]); // Agregar al conjunto
      }
    }
  } else {
    print('Error en la solicitud: ${response.statusCode}');
  }

  return additionalImageUrls.toList(); // Convertir el conjunto en una lista
}


  void _showPokemonDetail(int id) async {
    final additionalImageUrls = await fetchPokemonImages(id);
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          content: SizedBox(
            width: 200,
            child: ListView(
              shrinkWrap: true,
              children: [
                for (var imageUrl in additionalImageUrls)
                  Image.network(imageUrl),
              ],
            ),
          ),
        );
      },
    );
  }

@override
Widget build(BuildContext context) {
  return GridView.builder(
    gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
      crossAxisCount: 2, // Divide en dos columnas
      crossAxisSpacing: 100.0, // Espacio horizontal entre elementos
      mainAxisSpacing: 50.0, // Espacio vertical entre elementos
    ),
    itemCount: pokemonData.length,
    itemBuilder: (context, index) {
      final pokemon = pokemonData[index];
      final name = pokemon['name'] ?? '';
      return Card(
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(15.0),
        ),
        elevation: 100.0,
        margin: EdgeInsets.all(8.0),
        child: InkWell(
          onTap: () => _showPokemonDetail(int.parse(pokemon['id'] ?? '1')),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              Text(
                pokemon['name'] ?? '',
                style: TextStyle(fontSize: 28.0), // Tamaño de fuente más pequeño
              ),
              Text(
               '#'+(pokemon['id'] ?? ''),
                style: TextStyle(fontSize: 18.0), // Tamaño de fuente más pequeño
              ),
              SizedBox(height: 76.0), // Espacio entre el texto y la imagen
              Container(
                width: 150.0, // Tamaño de la imagen
                height: 150.0, // Tamaño de la imagen
                child: Center(
                  child: Transform.scale(
                    scale: 3, // Ajusta este valor para hacer la imagen más grande
                    child: Image.network(
                      pokemon['imageUrl'] ?? '',
                      fit: BoxFit.contain, // Ajusta la imagen al tamaño del contenedor escalado
                    ),
                  ),
                ),
              ),
              ElevatedButton(
            onPressed: () {
              _saveSelectedPokemon(name);
            },
            child: Text('Agregar a favoritos Pokémon'),
          ),
            ],
          ),
        ),
      );
    },
  );
}
void _saveSelectedPokemon(String pokemonName) {
  setState(() {
    selectedPokemonNames.add(pokemonName);
    print(selectedPokemonNames);
  });
}
}
