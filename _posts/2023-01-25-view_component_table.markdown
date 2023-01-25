---
layout: default
title: view component table
---
I have been trying [ViewComponent](https://viewcomponent.org/) for the UI in an application I wrote.

It has been great improvement for testing the little bits of the UI as well as many of other benefits.

It took me a while to figure out the best way to do a table using the gem, so I am noting my solution here.

First I need to create a component using the following files:

app/components/table_component.rb
```
class TableComponent < ApplicationComponent
  renders_many :headings
  renders_many :rows, TableRowComponent
end
```

app/components/table_component.html.erb
```
<table class="table align-middle table-striped listing">
  <thead class="table-primary">
    <tr>
      <% headings.each do |heading| %>
        <th> <%= heading %> </th>
      <% end %>
    </tr>
  </thead>

  <tbody>

  <% if !rows? %>
      <tr>
        <td scope="row" colspan="100%">
          <i>Empty</i>
        </td>
      </tr>
  <% else %>
    <% rows.each do |row| %>
      <%= row %>
    <% end %>
  <% end %>

  </tbody>
</table>
```

app/components/table_row_component.rb
```
class TableRowComponent < ApplicationComponent
  renders_many :columns

  def initialize(item:)
    @item = item
  end

  attr_reader :item
end
```

app/components/table_row_component.html.erb
```
<tr>
  <% columns.each do |col| %>
    <td> <%= col %> </td>
  <% end %>
</tr>
```


With the component in place I can now use it in a template to render, for example, a table of users with delete buttons.

```
<%= render(TableComponent.new) do |table| %>
  <% table.with_heading { "Name"  } %>
  <% table.with_heading { "Organisation" } %>
  <% table.with_heading { "" } %>
  <% users.each do |item| %>
    <% table.with_row(item: item) do |row| %>
      <% row.with_column { row.item.name } %>
      <% row.with_column { row.item.organisation.name } %>
      <% row.with_column do %>
        <%= render DeleteButtonComponent.new(path: users_path(row.item)) %>
      <% end %>
    <% end %>
  <% end %>
<% end %>
```
