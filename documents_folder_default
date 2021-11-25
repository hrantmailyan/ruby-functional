class DocumentsFolderDefault < ActiveRecord::Base
  default_scope -> { where(deactivated: false) }
  belongs_to :user, foreign_key: :created_by
  belongs_to :parent_folder, class_name: 'DocumentsFolderDefault', foreign_key: :parent_id
  has_many :child_folders, class_name: 'DocumentsFolderDefault', foreign_key: :parent_id
end
