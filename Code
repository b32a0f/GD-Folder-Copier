/**
 * [Docs & Refs]
 * https://developers.google.com/apps-script/reference/drive/
 * https://developers.google.com/apps-script/guides/services/advanced#enabling_advanced_services
 * https://developers.google.com/drive/api/v2/reference/
 * 
 * GAS, makeCopy()               https://ctrlq.org/code/19979-copy-folders-drive
 * GAS, getFolderById()          https://smartagent.blog/2018/07/06/appsscript03/
 * GAS, REST, Drive.Files.copy() https://stackoverflow.com/questions/52244239/drive-files-copy-and-parents-not-working
 * GAS, Ruby, API Distribution   https://www.44bits.io/ko/post/google-app-script-external-execution-by-ruby 
 * 
 * Py, API                       https://blog.psangwoo.com/coding/2017/07/10/google_drive_api.html
 * Py, API, Compare performance  https://blog.psangwoo.com/coding/2017/07/20/google_drive_api_compare.html
 * 
 * JSON, REST                    https://qiita.com/CUTBOSS/items/2ccb543f68f1a6c1aa7d
 * Other, OAuth                  https://qiita.com/TakahikoKawasaki/items/e37caf50776e00e733be
 * 
 * ※WARNING※
 * 1. File & Folder Iterator.next() Method selects items in order of created time inversely.
 * 2. Test before use. May not work as you want.
 * 3. It is toooo oooo oooo oooo oooo oooo oooo oooo slow.
 */

// [MAIN] to run.
// Before run, CHECK [folder IDs] and [mode] again.
function main() {
  /**
   * [MODE]
   * - Parent : Copy 'from' folder to under 'to' folder.
   * - Child  : Not 'from', just copy child of 'from'.
   * - Move_P : RESTRICT IN TEAM DRIVE. Actually not move. Copy and trash 'from'. Follow Parent mode.
   * - Move_C : RESTRICT IN TEAM DRIVE. Actually not move. Copy and trash 'from'. Follow Child mode.
   */
  
  /********************************************/
  var from = "";
  var to   = "";
  var mode = "Parent";
  /********************************************/
  
  Logger.log("[ＦＲＯＭ]\t: %s (%s)", DriveApp.getFolderById(from), from);
  Logger.log("[　ＴＯ　]\t: %s (%s)", DriveApp.getFolderById(to), to);
  Logger.log("[ＭＯＤＥ]\t: %s\n", mode);
  
  var fromtemp = DriveApp.getFolderById(from);
  
  // Check folder ID.
  if (from == to) {
    Logger.log("[ＦＡＩＬ]\t: Same folder!", 0);
    
    return -1;
  }
  
  // Mode switch.
  if (mode == "Parent") {
    copyParent(from, to);
  }
  else if (mode == "Child") {
    copyChild(from, to);
  }
  else if (mode == "Move_P") {
    copyParent(from, to);
    fromtemp.getParents().next().removeFolder(fromtemp);
  }
  else if (mode == "Move_C") {
    copyChild(from, to);
    fromtemp.getParents().next().removeFolder(fromtemp);
  }
  else {
    Logger.log("[ＦＡＩＬ]\t: Wrong mode!", 0);
    
    return -1;
  }
}

// Create new ID:parent folder.
// For Parent mode.
function copyParent(from, to) {
  var target = DriveApp.getFolderById(from);
  var dest   = DriveApp.getFolderById(to);
  
  // If folder is aleady exist.
  // Get old folder.
  if (dest.getFoldersByName(target.getName()).hasNext()) {
    dest = dest.getFoldersByName(target.getName()).next();
  }
  // If not, create new folder.
  else {
    dest = dest.createFolder(target.getName());
  }
  
  // Copy file.
  if (target.getFiles().hasNext()) {
    copyFile(target, dest);
  }
  
  // Sub Func call.
  if (target.getFolders().hasNext()) {
    copyChild(target.getId(), dest.getId());
  }
}

// Create new ID:child folder.
// Recursive Func.
function copyChild(target_ID, dest_ID) {
  var target = DriveApp.getFolderById(target_ID);
  var dest   = DriveApp.getFolderById(dest_ID);
  
  // If child folder is exist.
  if (target.getFolders().hasNext()) {
    var folderlist = target.getFolders();
    
    while (folderlist.hasNext()) {
      var folder = folderlist.next();
      
      // If folder is aleady exist.
      // Get old folder.
      if (dest.getFoldersByName(folder.getName()).hasNext()) {
        var exist = dest.getFoldersByName(folder.getName()).next();
        var temp  = DriveApp.getFolderById(exist.getId());
        
        Logger.log("<FOLDER>\tEXIST\t : %s", temp);
      }
      // If not, create new folder.
      else {
        var temp = dest.createFolder(folder.getName());
        
        Logger.log("<FOLDER>\tCREATE\t : %s", temp);
      }
      
      // Copy file.
      copyFile(folder, temp);
      
      // If child folder has child folder.
      if (folder.getFolders().hasNext()) {
        copyChild(folder.getId(), temp.getId());
      }
    }
  }
}

// Create new ID:file.
// Parameters are Folder, Not ID.
function copyFile(target, dest) {
  var filelist = target.getFiles();
  
  while (filelist.hasNext()) {
    var cur_file = filelist.next();
    
    // If file is aleady exist.
    if (dest.getFilesByName(cur_file.getName()).hasNext()) {
      var old_file = dest.getFilesByName(cur_file.getName()).next();
      
      // Compare file size.
      if (cur_file.getSize() == old_file.getSize()) {
        Logger.log("<FILE>\t\tEXIST\t : %s", cur_file);
      }
      else {
        cur_file.makeCopy(cur_file.getName() + "$$new$$", dest);
        
        Logger.log("<FILE>\t\tNEW\t : %s", cur_file);
      }
    }
    // If not exist.
    else {
      cur_file.makeCopy(dest);
      
      Logger.log("<FILE>\t\tCREATE\t : %s", cur_file);
    }
  }
}
