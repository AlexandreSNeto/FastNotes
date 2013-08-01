package br.eti.asneto.android.fastnotes;

import br.eti.asneto.android.fastnotes.db.NotesDbAdapter;
import android.app.AlertDialog;
import android.app.ListActivity;
import android.content.DialogInterface;
import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.ContextMenu.ContextMenuInfo;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.Window;
import android.widget.AdapterView.AdapterContextMenuInfo;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;

public class FastNotes extends ListActivity {
	
    private static final int ACTIVITY_CREATE = 0;
    private static final int ACTIVITY_EDIT = 1;
    private static final int ACTIVITY_VIEW = 2;
    
    private static final int MENU_INSERT = Menu.FIRST;
    private static final int MENU_ABOUT = Menu.FIRST + 1;
    private static final int MENU_EXIT = Menu.FIRST + 2;    
    
    private static final int CONTEXT_DELETE = Menu.FIRST;
    private static final int CONTEXT_EDIT = Menu.FIRST + 1;
    
    private NotesDbAdapter mDbHelper;
	    
    /**
     * Ao criar activity
     */
    @Override
    public void onCreate(Bundle savedInstanceState) {
    	super.onCreate(savedInstanceState);
    	requestWindowFeature(Window.FEATURE_CUSTOM_TITLE);
    	setContentView(R.layout.notes_list);
    	getWindow().setFeatureInt(Window.FEATURE_CUSTOM_TITLE, R.layout.title);

    	mDbHelper = new NotesDbAdapter(this);
        fillData();
        registerForContextMenu(getListView());
    }

    /**
     * Ao criar o menu de opções
     */
	@Override
    public boolean onCreateOptionsMenu(Menu menu) {
    	boolean result = super.onCreateOptionsMenu(menu);
        menu.add(0, MENU_INSERT, 0, R.string.menu_insert).setIcon(R.drawable.ic_menu_add);
        menu.add(0, MENU_ABOUT, 0, R.string.menu_about).setIcon(R.drawable.ic_menu_info);
        menu.add(0, MENU_EXIT, 0, R.string.menu_exit).setIcon(R.drawable.ic_menu_exit);
        return result;
    }

	/**
	 * Ao selecionar um item do menu de opções
	 */
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
        	case MENU_INSERT:
        		createNote();
        		return true;
        	case MENU_EXIT:
        		finish();
        		return true;
        	case MENU_ABOUT:
        		showAboutDialog();
        		return true;
        }
    	
        return super.onOptionsItemSelected(item);
    }
    
    /**
     * Exibe a dialog "About"
     */
    private void showAboutDialog() {
    	AlertDialog.Builder builder = new AlertDialog.Builder(this)
		.setMessage(R.string.about)
		.setNegativeButton("Ok", new DialogInterface.OnClickListener() {
			public void onClick(DialogInterface dialog, int id) {
				dialog.cancel();
			}
		});
    	builder.create().show();
    }
    
    /**
     * Ao criar o menu de contexto
     */
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenuInfo menuInfo) {
	    super.onCreateContextMenu(menu, v, menuInfo);
	    menu.add(0, CONTEXT_DELETE, 0, R.string.ctx_menu_delete);
	    menu.add(0, CONTEXT_EDIT, 0, R.string.ctx_menu_edit);
	    menu.setHeaderTitle(R.string.ctx_menu_title);
	    menu.setHeaderIcon(R.drawable.ico_ctx_menu);
	}

    /**
     * Ao selecionar um item do menu de contexto
     */
    @Override
	public boolean onContextItemSelected(MenuItem item) {
    	AdapterContextMenuInfo itemInfo = (AdapterContextMenuInfo) item.getMenuInfo();
        switch(item.getItemId()) {
        	case CONTEXT_DELETE:
                mDbHelper.open();
	            mDbHelper.deleteNote(itemInfo.id);
	            mDbHelper.close();
	            fillData();
	            return true;
        	case CONTEXT_EDIT:
        		editNote(itemInfo.id);
        		return true;
        }
        return super.onContextItemSelected(item);
	}    
    
    /**
     * Ao selecionar um item da lista
     */
    @Override
    protected void onListItemClick(ListView l, View v, int position, long id) {
        super.onListItemClick(l, v, position, id);
        viewNote(id);
    }

    /**
     * Chama a activity responsável por realizar a criação de um nova nota
     */
	private void createNote() {
		Intent i = new Intent(this, NotesEdit.class);
		startActivityForResult(i, ACTIVITY_CREATE);		
	}
	
	/**
	 * Chama a activity responsável por realizar atualizar a nota
	 * @param itemId
	 */
	private void editNote(long itemId) {
		Intent i = new Intent(this, NotesEdit.class);
        i.putExtra(NotesDbAdapter.KEY_ROWID, itemId);
        startActivityForResult(i, ACTIVITY_EDIT);	
	}
	
	/**
	 * Chama a activity responsável por visualizar uma nota
	 * @param itemId
	 */
	private void viewNote(long itemId) {
		Intent i = new Intent(this, NotesView.class);
        i.putExtra(NotesDbAdapter.KEY_ROWID, itemId);
        startActivityForResult(i, ACTIVITY_VIEW);	
	}

    /**
     * Ao retornar para este activity
     */
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent intent) {
        super.onActivityResult(requestCode, resultCode, intent);
        fillData();
    }  
    
	/**
	 * Preenche o listAdapter com os valores do banco
	 */
	private void fillData() {
		mDbHelper.open();
        Cursor mNotesCursor = mDbHelper.fetchAllNotes();
        
        startManagingCursor(mNotesCursor);
        
        String[] from = new String[]{NotesDbAdapter.KEY_TITLE, NotesDbAdapter.KEY_BODY, NotesDbAdapter.KEY_LASTUPDATEDATE};
        int[] to = new int[]{R.id.noteRowTopText, R.id.noteRowBottomText, R.id.noteRowBottomRigthText};
        
        SimpleCursorAdapter notes = new SimpleCursorAdapter(this, R.layout.note_row, mNotesCursor, from, to);
        setListAdapter(notes);
        mDbHelper.close();
	}
}