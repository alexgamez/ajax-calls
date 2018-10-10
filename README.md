## Crear Proyecto

`rails new ajax-calls --skip-spring --skip-test-unit --no-skip-test-unit --skip-coffee`


``rails g scaffold Book  title --no-controller-specs --no-view-specs --no-controller-test  --no-model-test  --no-system-test``

## New y Create con ajax

*app/controllers/books_controller.rb*
- format.js
En el metodo create

```ruby
class BooksController < ApplicationController
	.
	.
	.
  def index
    @books = Book.all
  end

  def new
    @book = Book.new
  end

  def create
    @book = Book.new(book_params)
    respond_to do |format|
      if @book.save
        format.js
      end
    end
  end
	.
	.
	.

  private
    def set_book
      @book = Book.find(params[:id])
    end

    def book_params
      params.require(:book).permit(:title)
    end
end

```


*app/models/book.rb*

```ruby
class Book < ApplicationRecord
  validates :title, presence: true
end

```

*app/views/books/index.html.erb*

- remote: true
En el llink_to a "New Book" 

-  <%= render 'book', book: book %>
llama al partial book.html.erb, el cual continua utilizando el objeto/referencia book

```ruby
<p id="notice"><%= notice %></p>

<h1>Books</h1>

<%= link_to 'New Book', new_book_path, remote: true , id: "new_book_link"%>

<div id="form_container">
</div>

<table>
  <thead>
    <tr>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody id="books_container">
    <% @books.each do |book| %>
      <%= render 'book', book: book %>
    <% end %>
  </tbody>
</table>


```

*app/views/books/_book.html.erb*
- book
lo recibe desde index

```ruby
<tr>
  <td><%=  book.title %></td>
  <td><%= link_to 'Show', book %></td>
  <td><%= link_to 'Edit', edit_book_path(book) %></td>
  <td><%= link_to 'Destroy', book, method: :delete, data: { confirm: 'Are you sure?' } %></td>
</tr>
```
*app/views/books/new.js.erb*

En este archivo esta el funcionamiento del la aplicacion despues de presionar el link_to new.

```ruby
// Ocultar link para agregar un nuevo registro
$("#new_book_link").hide()

// Colocar formulario para nuevo registro
$("#form_container").append( "<%= j (render 'form', book: @book) %>" )
```

*app/views/books/_form.html.erb*

Este archivo no es necesario editarlo, con el que crea el comando scaffold es suficiente.
```ruby
<%= form_with(model: book, remote: true) do |form| %>
  Title: 
  <%= form.text_field :title %>

  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>
```


*app/views/books/create.js.erb*

En este archivo esta el funcionamiento del la aplicacion despues de presionar el submit hacia el metodo create.

```ruby
// Mostrar link para agregar un nuevo registro
$("#new_book_link").show()

// Quitar formulario para nuevo registro
$("#form_container").empty();

// Agregar nuevo registros a la pantalla
$('#books_container').append('<%= j(render 'book', book: @book) %>');
```
