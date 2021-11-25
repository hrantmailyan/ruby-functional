
module Mutations
  module DocumentFolderDefaultMutations
    class DocumentFolderDefaultMoveMutation < Mutations::BaseMutation
      argument :attributes, Types::DocumentFolderDefaultTypes::DocumentFolderDefaultMoveAttributes, required: true
      argument :move_mode, Types::DocumentFolderDefaultTypes::DocumentFolderDefaultMoveMode, required: false
      field :document_folder_default, Types::DocumentFolderDefaultTypes::DocumentFolderDefaultType, null: true
      field :message, String, null:true
      def resolve(attributes:, move_mode: nil)
        attributes = attributes.to_h
        id = attributes[:id]
        parent_id = attributes[:new_parent_id]
        document_folder = DocumentsFolderDefault.find(id)
          parent_folder = DocumentsFolderDefault.where(parent_id: parent_id, name:document_folder.name).first
          num = 2
          if move_mode == 'check' && parent_folder &&  document_folder.name == parent_folder.name
            message = 'folders have same name '
            {
              document_folder_default: document_folder,
              message: message
            }
          elsif move_mode == 'check' && !parent_folder
              message = 'folder successfully moved '
              document_folder.parent_id = parent_id
              document_folder.save
              {
                document_folder_default: document_folder,
                message: message
              }
          elsif move_mode == 'keep'
            num = 2
            sum = 2
            is_exist = true
            while is_exist == true
              parent_folder = DocumentsFolderDefault.where(parent_id: parent_id, name:document_folder.name).pluck(:name)[0]
              if document_folder.name != parent_folder
                is_exist = false
              elsif document_folder.name == parent_folder
                if document_folder.name.include? " (#{sum})"
                  document_folder.name[" (#{sum})"] = " (#{sum +1})"
                  sum += 1
                else
                  document_folder.name = document_folder.name + " (#{num})"
                end
              end
              num += 1
            end
            document_folder.parent_id = parent_id
            document_folder.save
            message = 'folder name successfully changed '
            {
              document_folder_default: document_folder,
              message: message
            }
          elsif move_mode == 'replace'
              existing_folder = DocumentsFolderDefault.find_by(parent_id: parent_id, name:document_folder.name)
              if existing_folder
                existing_folder.update(deactivated: true)
                child_folders = DocumentsFolderDefault.where(parent_id: existing_folder.id)
                while child_folders.any?
                  child_folders.each do |child_folder|
                    child_folder.update(deactivated: true)
                  end
                  child_folders = DocumentsFolderDefault.where(parent_id: child_folders.pluck(:id))
                end
              end
              document_folder.parent_id = parent_id
              document_folder.save
            message = 'folder replaced'
            {
              document_folder_default: document_folder,
              message: message
            }
          else
            raise Exceptions::GraphQLForbiddenError, 'You must be an active member of that project to access it.'
          end
      end
    end
  end
end
