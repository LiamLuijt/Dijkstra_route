Als je met een echte geografische kaart wilt werken, hoef je gelukkig niet zelf alle straten en kruispunten ter wereld in te voeren. De gouden standaard hiervoor is OpenStreetMap (OSM), gecombineerd met de Python-bibliotheek OSMnx.
Met OSMnx kun je met één regel code het echte wegennetwerk van een stad downloaden, omzetten in een bruikbare graaf, en daar direct Dijkstra of A* op loslaten.
Hier is hoe je een programma bouwt dat de snelste route berekent over de echte straten van bijvoorbeeld Delft:

1. Benodigde pakketten installeren

Je hebt een aantal bibliotheken nodig om de kaartdata op te halen, de kortste route te berekenen en het resultaat visueel te tonen. Typ dit in je terminal:
bash
pip install osmnx networkx matplotlib shapely
Wees voorzichtig met code.

2. De Python Code voor een Echte Kaart

Dit script downloadt het wegennetwerk, zoekt de dichtstbijzijnde punten op de kaart voor je start- en eindlocatie, en berekent de snelste route op basis van de reistijd.
python
import osmnx as ox
import networkx as nx
import matplotlib.pyplot as plt

# 1. Download het wegennetwerk van een echte locatie (bijv. Delft)
# 'drive' zorgt ervoor dat we alleen wegen downloaden waar auto's mogen rijden
print("Kaart downloaden...")
G = ox.graph_from_place("Delft, Netherlands", network_type="drive")

# 2. Voeg snelheden en reistijden toe aan de wegen (edges)
# OSMnx schat de snelheid op basis van het type weg als deze ontbreekt
G = ox.add_edge_speeds(G)
G = ox.add_edge_travel_times(G)

# 3. Definieer de start- en eindcoördinaten (breedtegraad, lengtegraad)
# Voorbeeld: Van de TU Delft Aula naar de Markt in Delft
start_coords = (52.0016, 4.3715)  # TU Delft Aula
doel_coords = (52.0116, 4.3592)   # Markt Delft

# 4. Vind de dichtstbijzijnde knooppunten (nodes) op de kaart voor deze coördinaten
start_node = ox.nearest_nodes(G, X=start_coords[1], Y=start_coords[0])
doel_node = ox.nearest_nodes(G, X=doel_coords[1], Y=doel_coords[0])

# 5. Bereken de snelste route op basis van reistijd ('travel_time')
# Als je 'weight=length' gebruikt, krijg je de kortste route in meters
print("Snelste route berekenen...")
snelste_route = nx.shortest_path(G, source=start_node, target=doel_node, weight='travel_time')

# 6. Toon het resultaat op de echte kaart
print("Route plotten...")
fig, ax = ox.plot_graph_route(G, snelste_route, route_color="red", route_linewidth=5, node_size=0)
plt.show()
Wees voorzichtig met code.

Hoe dit achter de schermen werkt:

1. ox.graph_from_place: Haalt live data op van de OpenStreetMap API. Elk kruispunt wordt een node (met GPS-coördinaten) en elke straat een edge.
2. add_edge_travel_times: Berekent per straat hoe lang je erover doet. Formule: tijd = afstand / snelheid. Dit getal wordt als het gewicht aan de weg gehangen.
3. nx.shortest_path: Dit activeert het ingebouwde Dijkstra/A*-algoritme van NetworkX. Omdat we als gewicht travel_time hebben meegegeven, kiest hij de route die de minste seconden kost, in plaats van de minste meters.
Wil je hiermee een interactieve webinterface of app maken (bijvoorbeeld met een kaart waarop je kunt klikken), of wil je extra data gaan toevoegen zoals actuele files en verkeersdrukte?
