# SDK PHP para API V2 de Lojistas

Provê os componentes PHP para uso da API V2 de lojistas.

## Criando um API Client

Antes de utilizar as APIs, é necessário a criação de um client com as configurações de _base path_ e também as credenciais para acesso.

Abaixo segue o código de exemplo:

```php
require_once 'CNovaApiLojistaV2.php';

// Alterar a chave informada com o valor de client_id 
// e access_token disponíveis para sua APP
Configuration::$apiKey['client_id'] = 'll0rQx9SSszz';
Configuration::$apiKey['access_token'] = 'nllKgtXTMvzz';

$api_client = new ApiClient('http://sandbox.cnova.com/api/v2');
```

## APIs Disponíveis

A seguir, são apresentadas as APIs disponíveis para operação dos recursos expostos pela CNova.

### Loads API

API utilizada para realização de operações de carga.

```php
$loads = new LoadsApi($api_client);

// Criação de um novo produto

$p = new Product();

$p->sku_id = "CEL_LGG4";
$p->product_seller_id = "CEL";
$p->title = "Produto de testes LG G4";
$p->description = "<h2>O novo produto de testes</h2>, LG G4";
$p->brand = "LG";
$p->gtin = array("123456ft");
$p->categories = array("Teste>API");
$p->images = array("http://img.g.org/img1.jpeg");

$price = new SellerItemPrice();
$price->default = 1999.0;
$price->offer = 1799.0;

$p->price = $price;

$stock = new Stock();
$stock->quantity = 100;
$stock->cross_docking_time = 0;

$p->stock = $stock;

$dimensions = new Dimensions();
$dimensions->weight = 10;
$dimensions->length = 10;
$dimensions->width = 10;
$dimensions->height = 10;

$p->dimensions = $dimensions;

$pa = new ProductAttribute();
$pa->name = "cor";
$pa->value = "Verde";

$p->attributes =  array($pa);

// Adiciona o novo produto na lista a ser enviada
$products = array($p);

try {

	// Envia a carga de produtos
	$loads->postProducts($products);

} catch (ApiException $e) {
	var_dump($e->getResponseBody());
}
```

```php
$loads = new LoadsApi($api_client);

$created_at = $api_client->toString(new DateTime('NOW'));

$resp = $loads->getProducts("$created_at,*", null, 0, 5);
var_dump($resp);
```

### Seller Items API

API utilizada para gerenciamento dos recursos enviados pelo lojista.

```php
$seller_items = new SellerItemsApi($api_client);

$resp = $seller_items->getSellerItems(null, 0, 5);
var_dump($resp);
```

### Orders API

API utilizada para gerenciamento de pedidos.

```php
$orders = new OrdersApi($api_client);

// Prepara a atualização de traking
$body = new TrackingUpdate();

$it1 = new ItemTrackingUpdate();
$it1->sku_seller_id = "23258312-1";

$it2 = new ItemTrackingUpdate();
$it2->sku_seller_id = "23258312-2";

$body->items = array($it1, $it2);

$body->occurred_at = new DateTime('NOW');
$body->seller_delivery_id = "99995439701";
$body->number = "01092014";
$body->url = "servico envio2";
$body->cte = "010920141";

$c = new Carrier();
$c->name = "Sedex";
$c->cnpj = "72874279234";
$body->carrier = $c;

$i = new Invoice();
$i->cnpj = "72874279234";
$i->number = "123";
$i->serie = "456";
$i->issued_at = new DateTime('NOW');
$i->access_key = "01091111111111111111111111111111111111101092";
$i->link_xml = "xlm teste";
$i->link_danfe = "link nfe teste";
$body->invoice = $i;

try {

	// Envia a atualização de traking para a API
	$orders->postOrderTrackingSent($body, '987601');

} catch (ApiException $e) {
	var_dump($e->getResponseHeaders());
	var_dump($e->getResponseBody());
}
```

```php
$orders = new OrdersApi($api_client);

$resp = $orders->getOrders(0, 5);
foreach ($resp->orders as $order) {
	echo("$order->id - $order->status \n");
}
```

### Tickets API

API utilizada para gerenciamento de tickets.

```php
$tickets = new TicketsApi($api_client);

// Criação de um novo ticket
$new_ticket = new PostTicket();
$new_ticket->to = 'atendimento+OS_706000500000@mktp.extra.com.br';
$new_ticket->body = 'Corpo da mensagem do ticket';

try {
	$tickets->postTicket($new_ticket);
} catch (ApiException $e) {
	var_dump($e->getResponseBody());	
}
```

```php
$tickets = new TicketsApi($api_client);

$resp = $tickets->getTickets("Aberto", null, null, null, null, 0, 5);
var_dump($resp);
```

### Categories API

API utilziada na obtenção da árvore de categorias disponível.

```php
$categories = new CategoriesApi($api_client);

$resp = $categories->getCategories(0, 5);

foreach ($resp->categories as $category) {
	echo ("$category->id - $category->name \n");
}
```

### Sites API

API utilizada na obtenção da lista de sites.

```php
$sites = new SitesApi($api_client);
$resp = $sites->getSites();

var_dump($resp);
```

### Warehouses API

API utilizada na obtenção da lista de warehouses (armazéns).

```php
$warehouses = new WarehousesApi($api_client);
$resp = $warehouses->getWarehouses();

var_dump($resp);
```
