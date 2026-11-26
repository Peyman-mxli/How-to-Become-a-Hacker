# Ruby on Rails — Ruby Web Framework

Rails is an MIT-licensed MVC framework. Convention over configuration reduces repetitive decisions. Active Record maps models to database records; Action Controller manages requests; Action View renders templates; migrations define schema changes; Bundler resolves gems (Ruby packages). config/routes.rb defines routing, app/controllers contains controllers, app/models contains models, app/views contains templates, db/migrate contains migrations. Gemfile and Gemfile.lock define and lock dependencies.

## Original local route
config/routes.rb:
```ruby
Rails.application.routes.draw do
  get 'api/notices/:id', to: 'notices#show'
end
```
app/controllers/notices_controller.rb:
```ruby
class NoticesController < ApplicationController
  def show
    return render json: { error: 'Not found' }, status: :not_found unless params[:id] == '42'

    render json: { id: 42, content: 'Class notice' }
  end
end
```
get registers GET; :id captures the path; notices#show selects the action; params accesses request parameters; render json serializes the object; :not_found denotes 404. This is a fictional public-data endpoint.

## Commands in an existing project
```bash
ruby -v
bundle exec rails -v
bin/rails routes
bin/rails db:migrate:status
```
bundle exec uses project dependency context; routes lists mapped operations; migration status inspects schema history. Do not execute migrations on real systems without review.

## Security
Use scoped Active Record queries, strong parameters to allowlist writes, authorization checks, CSRF protections for cookie-authenticated forms, escaped templates, secrets management and supported dependencies. Never treat params[:id] as proof of ownership. Rails is an optional framework for future school applications; current projects are not Rails. [Official guides](https://guides.rubyonrails.org/).
