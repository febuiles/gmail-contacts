gmail-contacts
==============

A Ruby gem for getting a list of your Gmail contacts. Most of the code here has been extracted from
[mislav's contacts gem](https://github.com/mislav/contacts). It's been updated to work with Ruby 1.9
and Rails 3.

Installation
-------

    gem install gmail-contacts

or in your Gemfile:

    gem 'gmail-contacts', :require => "gmail-contacts"

Usage
-----
Get a link to the authorization URL:

    GmailContacts::Google.authentication_url("http://mysite.com/invites")

The user will be redirected to the URL you pass and a `token` parameter will be sent along. Capture
that token and then request the contacts:

    GmailContacts::Google.new("some_token").contacts

Every `Contact` has a name and email fields:

    GmailContacts::Google.new("some_token").contacts.each do |contact|
      puts "#{contact.name}: #{contact.email}"
    end

Usage with Rails
----
First create the authorization link in one of your views:

    # app/views/invites/new.html.erb
    <%= link_to "Invite your Gmail contacts", GmailContacts::Google.authentication_url("http://mysite.com/invites") %>

Then create a controller action that receives the token and fetches the contacts:

    # config/routes.rb
    match "/invites" => "invites#index"

    # app/controllers/invites_controller.rb
    class InvitesController
      def index
        token = params[:token]
        @contacts = GmailContacts::Google.new("some_token").contacts
      end
    end

Finally, iterate through the contacts in your view:

    # app/views/invites/index.html.erb
    <% @contacts.each do |contact| %>
      <span><strong><%= contact.name %></strong>: <% contact.email %></span>
    <% end %>
